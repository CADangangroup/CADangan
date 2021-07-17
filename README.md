# CADangan
This project about jumping dinosour game but implemented into the de2 board and using the verilog code


Group Members : 
1) Muhammad Qayyim bin Md Saleh         A17KE0187
2) Ahmad Arif Zahrin bin Md Tajuddin    A17KE0011
3) Muhammad Zulhusni bin Md Zaid        A17KE0204

# Introduction
Jumping Dinosour game on the offline browser already well known by many people, this game will pop out when the pc or laptop doesnt have internet connection.
How to play the game :
1) Dinosour will start run when button start is triggered.
2) Obstacle will pop out randomly time.
3) Dinosour will set the score follow the time that dino survived by jumping and avoid collision with the obstacles.
![image](https://user-images.githubusercontent.com/87574784/126040581-babefd77-2809-4db6-b022-bd2992889ecc.png)


# Software
Altera Quartus 13.0 (64 bit)

# Hardware 
De2 board Cyclone II ( EP2C35F672C6N )

# Datapath Unit


	module datapathUnit
	(
		CLOCK_50,						//	On Board 50 MHz
		// Your inputs and outputs here
        KEY,
        SW,
		  HEX0,
		  HEX1,
		  HEX2,
		  HEX3,
		  HEX4,
		  HEX5,
		// The ports below are for the VGA output.  Do not change.
		VGA_CLK,   						//	VGA Clock
		VGA_HS,							//	VGA H_SYNC
		VGA_VS,							//	VGA V_SYNC
		VGA_BLANK_N,						//	VGA BLANK
		VGA_SYNC_N,						//	VGA SYNC
		VGA_R,   						//	VGA Red[9:0]
		VGA_G,	 						//	VGA Green[9:0]
		VGA_B   						//	VGA Blue[9:0]
	);
	input			CLOCK_50;				//	50 MHz
	input   [9:0]   SW;
	input   [3:0]   KEY;
	
	output [6:0] HEX0, HEX1, HEX2, HEX3, HEX4, HEX5;

	// Declare your inputs and outputs here
	// Do not change the following outputs
	output			VGA_CLK;   				//	VGA Clock      
	output			VGA_HS;					//	VGA H_SYNC
	output			VGA_VS;					//	VGA V_SYNC
	output			VGA_BLANK_N;				//	VGA BLANK
	output			VGA_SYNC_N;				//	VGA SYNC
	output	[9:0]	VGA_R;   				//	VGA Red[9:0]
	output	[9:0]	VGA_G;	 				//	VGA Green[9:0]
	output	[9:0]	VGA_B;   				//	VGA Blue[9:0]
	
	
	wire resetn;
	assign resetn = KEY[0];
	
	// Create the colour, x, y and writeEn wires that are inputs to the controller.
	wire [2:0] colour;
	wire [7:0] x;
	wire [6:0] y;
	wire boxwriteEn, dinowriteEn, writeEn;
	wire [7:0] outx3, outx2, outx1, outx0;
	wire [6:0] outy3, outy2, outy1, outy0, box_y;
	wire clock4out, clock60out, erase_box, draw0, draw1, draw2, draw3, draw_dino;
	wire [3:0] box_en;
	wire [3:0] x_column;
	wire column_erase_en, new_y_en;
	wire [6:0] generated_y;
	wire jump_sig;
	wire [3:0] counter10, hex1out, hex2out, hex3out, hex4out, hex5out;
	wire hex1sig, hex2sig, hex3sig, hex4sig, hex5sig;
	wire [6:0] h0out, h1out, h2out, h3out, h4out, h5out;
	wire collided;
	wire [7:0] dino_x;
	wire [6:0] dino_y;
	wire [2:0] dino_c;
	wire game_en;
	wire pause_dino;
	wire load_colour, enable;
	wire enable_hexes;
	wire enable2;


		clock4ps clock_4(clock60out, resetn, 1'b1, clock4out);
		clock60ps clock_60(CLOCK_50, resetn, 1'b1, clock60out);
		column_counter c0(clock4out, resetn, erase_box, x_column, column_erase_en);
		
		count10seconds hex0(CLOCK_50, resetn, enable, counter10, enable_hexes);
		
		assign hex1sig = (counter10 == 4'd0 && enable_hexes) ? 1 : 0 ;
		assign hex2sig = (hex1out == 4'd0) ? 1 : 0 ;
		assign hex3sig = (hex2out == 4'd0) ? 1 : 0 ;
		assign hex4sig = (hex3out == 4'd0) ? 1 : 0 ;
		assign hex5sig = (hex4out == 4'd0) ? 1 : 0 ;
		
		counttens hex1(hex1sig, resetn, enable, hex1out);
		counttens hex2(hex2sig, resetn, enable, hex2out);
		counttens hex3(hex3sig, resetn, enable, hex3out);
		counttens hex4(hex4sig, resetn, enable, hex4out);
		counttens hex5(hex5sig, resetn, enable, hex5out);
		
		hexdecoder h0(counter10, h0out);
		hexdecoder h1(hex1out, h1out);
		hexdecoder h2(hex2out, h2out);
		hexdecoder h3(hex3out, h3out);
		hexdecoder h4(hex4out, h4out);
		hexdecoder h5(hex5out, h5out);
		
		assign HEX0 = h0out;
		assign HEX1 = h1out;
		assign HEX2 = h2out;
		assign HEX3 = h3out;
		assign HEX4 = h4out;
		assign HEX5 = h5out;
		
		
		

		
		box_dino bd0(outx3, outy3, outx2, outy2, outx1, outy1, outx0, outy0, x_column, box_y, dino_x, dino_y, dino_c, CLOCK_50, resetn, draw0, draw1, draw2, draw3, draw_erase, dinowriteEn, boxwriteEn, writeEn, x, y, colour);
		
		box_control bc0(clock4out, box_en, column_erase_en, CLOCK_50, resetn, draw0, draw1, draw2, draw3, draw_erase, boxwriteEn, pause_dino);
		boxes_tracker b0(collided,new_y_en, generated_y, clock4out, resetn, outx3, outy3, outx2, outy2, outx1, outy1, outx0, outy0, box_en, erase_box, box_y);
		
		random_height rh0(CLOCK_50, clock4out, resetn, 1'b1, generated_y, new_y_en);
		
		dino_control c(pause_dino, enable, CLOCK_50, resetn, ~KEY[1], load_colour, dinowriteEn); 
		dino_datapath d(enable2, CLOCK_50, load_colour, resetn, jump_sig, dino_x, dino_y, dino_c);
	

		assign jump_sig = KEY[3];
		
		collision coll(outx3, outy3, outx2, outy2, outx1, outy1, outx0, outy0, dino_x, dino_y, collided);
		
		assign enable2 = (collided == 0) ? enable : 0;

	endmodule

	module collision(inx3, iny3, inx2, iny2, inx1, iny1, inx0, iny0, dino_x, dino_y, collision_detected);
	input [7:0] inx3, inx2, inx1, inx0;
	input [6:0] iny3, iny2, iny1, iny0;
	input [7:0] dino_x;
	input [6:0] dino_y;
	output reg collision_detected;
	
	always @(*) begin
		if (inx3 == dino_x + 3) begin
			if (dino_y + 3 > iny3)
				collision_detected = 1'b1;
			else 
				collision_detected = 1'b0;
		end
		else if (inx2 == dino_x + 3) begin
			if (dino_y + 3 > iny2)
				collision_detected = 1'b1;
			else 
				collision_detected = 1'b0;
		end
		else if (inx1 == dino_x + 3) begin
			if (dino_y + 3 > iny1)
				collision_detected = 1'b1;
			else 
				collision_detected = 1'b0;
		end
		else if (inx0 == dino_x + 3) begin
			if (dino_y + 3 > iny0)
				collision_detected = 1'b1;
			else 
				collision_detected = 1'b0;
		end
		else
			collision_detected = 1'b0;
	end

	endmodule
			
	

	module box_dino(inx3, iny3, inx2, iny2, inx1, iny1, inx0, iny0, inx_erase, iny_erase, dino_x, dino_y, dino_c, clock50mil, resetn, draw0, draw1, draw2, draw3, column_en, 	 dinowriteEn, boxwriteEn, writeEn, outx, outy, outc);
	input [7:0] inx3, inx2, inx1, inx0;
	input [6:0] iny3, iny2, iny1, iny0;
	input [3:0] inx_erase;
	input [6:0] iny_erase;
	input draw0, draw1, draw2, draw3, column_en, clock50mil, resetn, dinowriteEn, boxwriteEn;
	
	output reg [7:0] outx;
	output reg [6:0] outy;
	output reg [2:0] outc;
	output reg writeEn;
	
	input [7:0] dino_x;
	input [6:0] dino_y;
	input [2:0] dino_c;
	wire [7:0] box_x;
	wire [6:0] box_y;
	wire [2:0] box_c;
	
	combined_boxdatapaths cb(inx3, iny3, inx2, iny2, inx1, iny1, inx0, iny0, inx_erase, iny_erase, clock50mil, resetn, draw0, draw1, draw2, draw3, column_en, box_x, box_y, box_c);

	always @(*) begin
		if (draw0 || draw1 || draw2 || draw3 || column_en) begin
			outx = box_x;
			outy = box_y;
			outc = box_c;
		end
		else if (dinowriteEn) begin
			outx = dino_x;
			outy = dino_y;
			outc = dino_c;
		end
		else begin
			outx = 0;
			outy = 0;
			outc = 0;
		end
	end
	
	always @(*) begin
		if (dinowriteEn || boxwriteEn)
			writeEn = 1'b1;
		else 
			writeEn = 1'b0;
	end
	endmodule




	module combined_boxdatapaths(inx3, iny3, inx2, iny2, inx1, iny1, inx0, iny0, inx_erase, iny_erase, clock50mil, resetn, draw0, draw1, draw2, draw3, column_en, x, y, c);
	input [7:0] inx3, inx2, inx1, inx0;
	input [6:0] iny3, iny2, iny1, iny0;
	input [3:0] inx_erase;
	input [6:0] iny_erase;
	input draw0, draw1, draw2, draw3;
	input column_en;
	input clock50mil, resetn;
	output reg [7:0] x;
	output reg [6:0] y;
	output reg [2:0] c;
	
	wire [7:0] erasex; 
	wire [7:0] x1, x2, x3, x0, x_erase;
	wire [6:0] y1, y2, y3, y0, y_erase;
	wire [2:0] c0, c1, c2, c3;
	wire [2:0] c_erase;
	wire [2:0] white;
	assign white = 3'b011;
	assign erasex = {4'b0000, inx_erase};
	
	datapath d0(draw0, inx0, iny0, white, clock50mil, resetn, x0, y0, c0);	
	datapath d1(draw1, inx1, iny1, white, clock50mil, resetn, x1, y1, c1);
	datapath d2(draw2, inx2, iny2, white, clock50mil, resetn, x2, y2, c2);
	datapath d3(draw3, inx3, iny3, white, clock50mil, resetn, x3, y3, c3);
	draw_column e0(clock50mil, erasex, iny_erase, 1'b0, resetn, column_en, x_erase, y_erase, c_erase); //create a column eraser
	
	
	always @(*) begin
		if (draw0) begin
			x = x0;
			y = y0;
			c = c0;
		end
		else if (draw1) begin
			x = x1;
			y = y1;
			c = c1;
		end
		else if (draw2) begin
			x = x2;
			y = y2;
			c = c2;
		end
		else if (draw3) begin
			x = x3;
			y = y3;
			c = c3;
		end
		else if (column_en) begin
			x = x_erase;
			y = y_erase;
			c = c_erase;
		end
		else begin
			x = 0;
			y = 0;
			c = 0;
		end
	end
	endmodule
		

	module box_control(clock4ps, enable, erase_box, clock50mil, resetn, draw0, draw1, draw2, draw3, erase_en, plot, pause_dino);
	input [3:0]enable; // enable signal for each box
	input clock4ps, clock50mil, resetn, erase_box;
	
	output reg plot;
	output reg draw0, draw1, draw2, draw3, erase_en, pause_dino;
	
	reg [2:0] current_state, next_state;
	
	wire [9:0] q;
	wire clock_200;
	
	counter c200(clock50mil, resetn, 1'b1, q);
	assign clock_200 = (q == 10'd0) ? 1 : 0; // one high for every 200 pixels 
	
	localparam hold = 3'd0, box0 = 3'd1, box1 = 3'd2, box2 = 3'd3, box3 = 3'd4, erase = 3'd5, pause = 3'd6;
	
	always @(*)
		begin : state_table
			case (current_state)
				hold: next_state = (clock4ps) ? box0 : hold;
				box0: next_state = box1;
				box1: next_state = box2;
				box2: next_state = box3;
				box3: next_state = erase_box ? erase : pause; // if there is a box to erase go to erase state, else skip to pause state
				erase: next_state = pause;
				pause: next_state = clock4ps ? pause : hold; 
			default: next_state = hold;
		endcase
	end
	
	always @(*) begin
		plot = 1'b0;
		pause_dino = 1'b0;
		draw0 = 1'b0;
		draw1 = 1'b0;
		draw2 = 1'b0;
		draw3 = 1'b0;
		erase_en = 1'b0;
		
		case (current_state)
			hold: begin
				plot = 1'b0;
				pause_dino = 1'b0;
				draw0 = 1'b0;
				draw1 = 1'b0;
				draw2 = 1'b0;
				draw3 = 1'b0;
				erase_en = 1'b0;
			end
			box0: begin
				if (enable[0]) begin
					plot = 1'b1;
					pause_dino = 1'b1;
					draw0 = 1'b1;
					draw1 = 1'b0;
					draw2 = 1'b0;
					draw3 = 1'b0;
					erase_en = 1'b0;
				end
			end
			box1: begin
				if (enable[1]) begin
					plot = 1'b1;
					pause_dino = 1'b1;
					draw0 = 1'b0;
					draw1 = 1'b1;
					draw2 = 1'b0;
					draw3 = 1'b0;
					erase_en = 1'b0;
				end
			end
			box2: begin
				if (enable[2]) begin
					plot = 1'b1;
					pause_dino = 1'b1;
					draw0 = 1'b0;
					draw1 = 1'b0;
					draw2 = 1'b1;
					draw3 = 1'b0;
					erase_en = 1'b0;
				end
			end
			box3: begin	
				if (enable[3]) begin
					plot = 1'b1;
					pause_dino = 1'b1;
					draw0 = 1'b0;
					draw1 = 1'b0;
					draw2 = 1'b0;
					draw3 = 1'b1;
					erase_en = 1'b0;
				end
			end
			erase: begin
				plot = 1'b1;
				pause_dino = 1'b1;
				draw0 = 1'b0;
				draw1 = 1'b0;
				draw2 = 1'b0;
				draw3 = 1'b0;
				erase_en = 1'b1;
			end
			pause: begin
				plot = 1'b0;
				pause_dino = 1'b0;
				draw0 = 1'b0;
				draw1 = 1'b0;
				draw2 = 1'b0;
				draw3 = 1'b0;
				erase_en = 1'b0;
			end
		endcase
	end
		
			
	always @(posedge clock_200)
	begin: state_FFs
		if (!resetn)
			current_state <= hold;
		else
			current_state <= next_state;
	end
	
	endmodule

	module datapath(box_en, inx, iny, inc, clock50mil, resetn, outx, outy, outc);
	input [7:0] inx;
	input [6:0] iny;
	input [2:0] inc;
	input box_en, clock50mil, resetn;
	output reg [7:0] outx;
	output reg [6:0] outy;
	output reg [2:0] outc;
	reg [7:0] tempx;
	reg [6:0] tempy;
	reg [2:0] tempc;
	
	wire [3:0] c1;
	wire [5:0] c2;
	
	wire [7:0] dx;
	wire [6:0] dy;
	wire [2:0] dc;
	reg [5:0] height;
	// heights = y = 60 (60 pixels long), 70 (50 pixels long), 90 ( 30 pixels long) 
	
	draw_column d0(clock50mil, inx, iny, 1'b1, resetn, box_en, dx, dy, dc);
	
	always @(posedge clock50mil) begin
		if (!resetn) begin
			tempx <= 8'b0;
			tempy <= 7'b0;
		end
		else if (box_en) begin
			tempx <= inx;
			tempy <= iny;
		end
	end
	
	always @(*) begin
		if (iny == 60)
			height = 6'd60;
		else if (iny == 70)
			height = 6'd50;
		else if (iny == 90)
			height = 6'd30;
		else 
			height = 6'd0;
	end
	
	counter_x count_x(clock50mil, resetn, box_en, c1);
	assign enable1 = (c1 == 4'd11) ? 1 : 0; // y only increments if x is 0 
	counter_y count_y(clock50mil, height, resetn, enable1, c2);
	
	always @(*) begin
		if (c1 == 4'd11 || c1 == 4'd12)
			tempc = 3'b0; // colour the rightmost column black to erase it
		else if (!box_en)  // box is empty 
			tempc = 3'd0;
		else 
			tempc = inc;
	end
	
	always @(*) begin
		if (inx <= 160 && inx >= 152) begin
			outx = dx;
			outy = dy;
			outc = dc;
		end
		else begin
		   outx = tempx + c1; // needs to be 10 bits wide
		   outy = tempy + c2; // needs to be 10 bits wide  
			outc = tempc;
		end
	end
	
	endmodule

	module draw_column(clock50mil, inx, iny, select, resetn, draw_en, outx, outy, outc);
	input [7:0] inx; // given x, y coord, erase that column starting at (x,y)
	input [6:0] iny;
	input clock50mil, resetn, select, draw_en;
	output [7:0] outx;
	output [6:0] outy;
	output [2:0] outc;
	
	reg [7:0] tempx;
	reg [6:0] tempy;
	reg [2:0] tempc;
	wire [5:0] c2; // counts to 20
	reg [5:0] height;
	
	always @(posedge clock50mil) begin
		if (!resetn) begin
			tempx <= 8'b0;
			tempy <= 7'b0;
		end
		else if (draw_en) begin
			tempx <= inx;
			tempy <= iny;
		end
	end
	
	always @(*) begin
		if (iny == 90)
			height = 6'd30;
		else if (iny == 70)
			height = 6'd50;
		else if (iny == 60)
			height = 6'd60;
		else 
			height = 6'd10;
	end
	
	counter_y county(clock50mil, height, resetn, draw_en, c2);
	
	always @(*) begin
		if (!select)
			tempc = 3'b000; // if select == 0, we are erasing
		else if (select)
			tempc = 3'b011; // if select == 1, box appearing, colour it cyan
	end
	
	assign outx = tempx;
	assign outy = tempy + c2;
	assign outc = tempc; // column should be coloured black
			
	endmodule

	module column_counter(clock4ps, resetn, enable, q, column_erase_en);
	input clock4ps, resetn, enable;
	output reg [3:0] q;
	output reg column_erase_en;
	
	always @(posedge clock4ps, negedge resetn) begin
		if (!resetn) begin
			q <= 4'b0;
			column_erase_en <= 1'b0;
		end
		else if (q == 4'b0 && enable) begin
			q <= 4'd11;
			column_erase_en <= 1;
		end
		else if (q != 4'b0) begin
			q <= q - 1;
			column_erase_en <= 1;
		end
		else
			column_erase_en <= 0;
	end
	endmodule
	

	module boxes_tracker(collided, enablenew, iny, clock_4ps, resetn, outx3, outy3, outx2, outy2, outx1, outy1, outx0, outy0, box, erase_box, box_y); 
	input enablenew, clock_4ps, resetn, collided;
	input [6:0]iny;
	output reg [7:0] outx3, outx2, outx1, outx0; 
	output reg [6:0] outy3, outy2, outy1, outy0; // 19:10 = x, 9:0 = y
	output reg [3:0]box;
	output reg [6:0] box_y;
	output reg erase_box;
	wire [7:0] y;
	reg [7:0] box_array [0:3][0:1]; // 
	integer i;
	
	assign y = {1'b0, iny};
	
	initial begin // initialize box array with values all 0
		for (i = 0; i < 4; i = i + 1) begin
			box_array[i][1] = 7'd0;
			box_array[i][0] = 7'd0;
			box[i] = 0;
		end
		erase_box = 1'b0;
	end
		
	// x is [0], y is [1]
	// NOTE: x coord shifting happens on neg clock edge. Adding new value/ shifting box values occurs on posedge
	always @(posedge clock_4ps) begin
		if (collided) begin
		end
		
		else if (enablenew) begin // if enable new is high, put in box leaving screen, else look for empty box to put in
			if (box[3] && box_array[3][0] == 0) begin
				erase_box <= 1'b1; 
				box_array[3][0] <= 8'd160;
				box_array[3][1] <= y;
				for (i = 0; i < 4; i = i + 1) begin // left shift non empty boxes
					if (i != 3 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (box[2] && box_array[2][0] == 0) begin
				erase_box <= 1'b1;	
				box_array[2][0] <= 8'd160;
				box_array[2][1] <= y;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 2 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (box[1] && box_array[1][0] == 0) begin
				erase_box <= 1'b1;
				box_array[1][0] <= 8'd160;
				box_array[1][1] <= y;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 1 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (box[0] && box_array[0][0] == 0) begin
				erase_box <= 1'b1;
				box_array[0][0] <= 8'd160;
				box_array[0][1] <= y;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 0 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (!box[3]) begin// else put in one of the empty boxes
				erase_box <= 1'b0;
				box_array[3][0] <= 8'd160;
				box_array[3][1] <= y;
				box[3] <= 1;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 3 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (!box[2]) begin
				erase_box <= 1'b0;
				box_array[2][0] <= 8'd160;
				box_array[2][1] <= y;
				box[2] <= 1;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 2 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (!box[1]) begin
				erase_box <= 1'b0;
				box_array[1][0] <= 8'd160;
				box_array[1][1] <= y;
				box[1] <= 1;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 1 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (!box[0]) begin
				erase_box <= 1'b0;
				box_array[0][0] <= 8'd160;
				box_array[0][1] <= y;
				box[0] <= 1;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 0 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (box[0] && box[1] && box[2] && box[3] && box_array[3][0] != 0 && box_array[2][0] != 0 && box_array[1][0] != 0 && box_array[0][0] != 0)
			begin
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 0 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
				erase_box <= 1'b0;
			end
		end
		else begin// enable new not high, nothing new to add -> shift existing boxes
			if (box[3] && box_array[3][0] == 0) begin
				erase_box <= 1'b1;
				box_array[3][0] <= 8'd0;
				box_array[3][1] <= 8'd0;
				box[3] <= 0;
				for (i = 0; i < 3; i = i + 1) begin
					if (box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (box[2] && box_array[2][0] == 0) begin
				erase_box <= 1'b1;
				box_array[2][0] <= 8'd0;
				box_array[2][1] <= 8'd0;
				box[2] <= 0;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 2 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (box[1] && box_array[1][0] == 0) begin
				erase_box <= 1'b1;
				box_array[1][0] <= 8'd0;
				box_array[1][1] <= 8'd0;
				box[1] <= 0;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 1 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else if (box[0] && box_array[0][0] == 0) begin
				erase_box <= 1'b1;
				box_array[0][0] <= 8'd0;
				box_array[0][1] <= 8'd0;
				box[0] <= 0;
				for (i = 0; i < 4; i = i + 1) begin
					if (i != 0 && box[i])
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
			else begin // no box leaving, increment each by 1
				erase_box <= 1'b0;
				for (i = 0; i < 4; i = i + 1) begin
					if (box[i]) // box i not empty
						box_array[i][0] <= box_array[i][0] - 1'b1;
				end
			end
		end
	end
	
	always @(negedge clock_4ps) begin
		if (box_array[3][0] - 1 == 0)
			box_y = box_array[3][1];
		else if (box_array[2][0] - 1 == 0)
			box_y = box_array[2][1];
		else if (box_array[1][0] - 1 == 0)
			box_y = box_array[1][1];
		else if (box_array[0][0] - 1 == 0)
			box_y = box_array[0][1];
	end

	always @(*) begin
		outx3 = box_array[3][0]; 
		outx2 = box_array[2][0]; 
		outx1 = box_array[1][0]; 
		outx0 = box_array[0][0];
		
		outy3 = box_array[3][1][6:0]; 
		outy2 = box_array[2][1][6:0]; 
		outy1 = box_array[1][1][6:0]; 
		outy0 = box_array[0][1][6:0]; 
		
	end
	
	endmodule

	module clock60ps(clock,resetn,enable,out); // one high every 1/60th second
	input clock, resetn, enable;
	reg [19:0] q;
	output reg out;
		
	always @(posedge clock, negedge resetn) begin
		if(!resetn) begin
			q <= 20'd833333; //one high every 833,333 cycles
	//			q <= 20'd2000;
			out <= 0;
		end
		else if (enable == 1'b1) begin
			if ( q == 0 ) begin
				q <= 20'd83333;
	//				q <= 20'd2000;
				out <= 1;
			end
			else begin
				q <= q - 1;
				out <= 0;
			end
		end
	end
	endmodule

	module clock4ps(clock60ps, resetn, enable, out); // rate counter 4 pixels per second 
	input clock60ps, resetn, enable;
	reg [3:0] q;
	output reg out;
		
	always @(posedge clock60ps, negedge resetn) begin
		if (!resetn) begin
			q <= 4'b1111; // 15
			out <= 0;
		end
		else if (enable == 1'b1) begin
			if (q == 0) begin
				q <= 4'b1111;
				out <= 1;
			end
			else begin
				q <= q - 1;
				out <= 0;
			end
		end
	end
	endmodule
					
	module clocktest(clock50, resetn, enable, out60, out4);
	input clock50, resetn, enable;
	output out60, out4;
	wire outc60, outc4;
	
	clock60ps c60(clock50, resetn, enable, outc60);
	clock4ps c4(outc60, resetn, enable, outc4);
	
	assign out60 = outc60;
	assign out4 = outc4;
	endmodule

	module counter_x(clock, reset_n, enable, q); // count to 10 + 1 TEMP WIDTH 
	input clock, reset_n, enable;
	output reg 	[3:0] q;
	
	always @(posedge clock) begin
		if(reset_n == 1'b0)
			q <= 4'd12;
		else if (enable == 1'b1)
		begin
		  if (q == 4'd11)
			  q <= 4'd0;
			else if (q == 4'd12)
				q <= 4'd0;
		  else
			  q <= q + 1;
		end
	   end
	endmodule

	module counter_y(clock, height, reset_n, enable, q); // count to 20 TEMP HEIGHT
	input clock, reset_n, enable;
	input [5:0] height;
	output reg 	[5:0] q;
	
	always @(posedge clock) begin
		if(reset_n == 1'b0)
			q <= 6'd0;
		else if (enable == 1'b1)
		begin
		  if (q == height)
			  q <= 6'd0;
		  else
			  q <= q + 1;
		end
 	  end
	endmodule
	
	module counter(clock, resetn, enable, q); // counts to 240 i.e. amount of time to draw 1 box
	input clock, resetn, enable;
	output reg [9:0] q;
	
	always @(posedge clock) begin
		if (!resetn)
			q <= 10'd0;
		else if (enable) begin
			if (q == 10'd800)
			 q <= 10'd0;
			else
				q <= q + 1;
		end
	end
	endmodule

	module count_box_enable(clock4ps, resetn, enable, q);// counts 60 cycles of 4ps i.e. amount of time between boxes
	input clock4ps, resetn, enable;
	output reg [7:0] q;
	
	always @(posedge clock4ps) begin
		if (!resetn)
			q <= 8'd0;
		else if (enable) begin
			if (q == 8'd60)
			 q <= 8'd0;
			else
				q <= q + 1;
		end
	end
	endmodule

	module random_height(CLOCK_50, clock4ps, resetn, enable, out_random_y, new_y_enable);
	input CLOCK_50, clock4ps, resetn, enable;
	output reg [6:0] out_random_y;
	output reg new_y_enable;
	wire [27:0] out50;
	wire [9:0] q;
	
	rate_divider rd(enable, 10'd60, clock4ps, resetn, q);
	counterto50mil c50(CLOCK_50, resetn, enable, out50);
	
	always @(posedge clock4ps) begin
		if (!resetn) begin
			new_y_enable <= 0;
			out_random_y <= 0;
		end
	
		else if (q == 10'd59) begin
			new_y_enable <= 1;
			if (out50 >= 0 && out50 <= 16666667)
				out_random_y <= 7'd60;
			else if (out50 > 16666667 && out50 <= 33333333)
				out_random_y <= 7'd70;
			else if (out50 > 33333333)
				out_random_y <= 7'd90;
		end
		else 
			new_y_enable <= 0;
	end
	
	endmodule

	module rate_divider(enable, data, clk, reset, q);
	input enable, clk, reset;
	input [9:0] data;
	output reg [9:0] q;
	
	always @(posedge clk, negedge reset)
	begin
		if (~reset)
			q <= data;
		else if (enable)
			begin
				if (q == 0)
					q <= data;
				else
					q <= q - 1'b1;
			end
	end
	endmodule

	module counterto50mil(CLOCK_50, resetn, enable, q);
	input CLOCK_50, resetn, enable;
	output reg [27:0] q;
	
	always @(posedge CLOCK_50, negedge resetn) begin
		if (!resetn)
			q <= 0;
		else if (enable) begin
			if (q == 28'd50000000)
				q <= 0;
			else
				q = q + 1;
		end
	end
	endmodule


	module count10seconds(CLOCK_50, resetn, enable, tencounter, enable_hexes); // outputs how many seconds have passed up to 10
	input CLOCK_50, resetn, enable;
	reg [27:0] q;
	output reg [3:0] tencounter;
	output reg enable_hexes;
	//	output reg [3:0] h1, h2, h3, h4, h5;
	
	always @(posedge CLOCK_50) begin
		if (!resetn)
			q <= 0;
		else if (enable) begin
			if (q == 28'd49999999)
				q <= 0;
			else
				q = q + 1;
		end
	end
	
	always @(posedge CLOCK_50) begin
		if (!resetn) begin
			tencounter <= 0;
			enable_hexes <= 0;
		end

		else if (enable) begin
			if (q == 28'd49999999) begin
				if (tencounter == 4'd9) begin
					tencounter <= 0;
					enable_hexes <= 1;
				end

				else begin
				 tencounter <= tencounter + 1;
				 enable_hexes <= 1;
				end
			end
		end
	end	
	endmodule

	module counttens(clock, resetn, enable, q);
	input clock, resetn, enable;
	output reg [3:0] q;
	
	always @(posedge clock, negedge resetn) begin
		if (!resetn)
			q <= 0;
		else if (enable) begin
			if (q == 4'd9)
				q <= 0;
			else 
				q = q + 1;
		end
	end
	endmodule


	module test_rh(CLOCK_50, resetn, out_y, out_enable);
	input CLOCK_50, resetn;
	output [6:0] out_y;
	output out_enable;
	
	wire clock4out, clock60out;
	wire [6:0] y;
	wire en;
	
	clock4ps clock_4(clock60out, resetn, 1'b1, clock4out);
	clock60ps clock_60(CLOCK_50, resetn, 1'b1, clock60out);
	random_height rh(CLOCK_50, clock4out, resetn, 1'b1, y, en);
	
	assign out_y = y;
	assign out_enable = en;
	endmodule

	module control_combined_datapath(inx3, iny3, inx2, iny2, inx1, iny1, inx0, iny0, CLOCK_50, resetn, box_en, x, y, c, plot);
	input [7:0] inx3, inx2, inx1, inx0;
	input [6:0] iny3, iny2, iny1, iny0;
	input [3:0] box_en;
	input CLOCK_50, resetn;
	
	output [7:0] x;
	output [6:0] y;
	output [2:0] c;
	output plot;
	
	wire [7:0] xout;
	wire [6:0] yout;
	wire [2:0] cout;
	wire draw0, draw1, draw2, draw3;
	wire clock4out, clock60out;
	
	clock60ps c60(CLOCK_50, resetn, 1'b1, clock60out);
	clock4ps c4(clock60out, resetn, 1'b1, clock4out);
	box_control control(clock4out, box_en, CLOCK_50, resetn, draw0, draw1, draw2, draw3, plot);
	combined_boxdatapaths c_datapaths0(inx3, iny3, inx2, iny2, inx1, iny1, inx0, iny0, CLOCK_50, resetn, draw0, draw1, draw2, draw3, xout, yout, cout);
	assign x = xout;
	assign y = yout;
	assign c = cout;
	
	endmodule

	module all (CLOCK_50, resetn, draw_new, key, x, y, colour, writeEn);
	input CLOCK_50, resetn, draw_new, key;
	output [7:0] x;
	output [6:0] y;
	output [2:0] colour;
	output writeEn;
	wire boxwriteEn, dinowriteEn;
	wire [7:0] outx3, outx2, outx1, outx0;
	wire [6:0] outy3, outy2, outy1, outy0, box_y;
	wire clock4out, clock60out, erase_box, draw0, draw1, draw2, draw3, draw_dino;
	wire [3:0] box_en;
	wire [3:0] x_column;
	wire column_erase_en, new_y_en;
	wire [6:0] generated_y;
	wire jump_sig;

	
	clock4ps clock_4(clock60out, resetn, 1'b1, clock4out);
	clock60ps clock_60(CLOCK_50, resetn, 1'b1, clock60out);
	column_counter cc0(clock4out, resetn, erase_box, x_column, column_erase_en);
	
	box_dino bd0(outx3, outy3, outx2, outy2, outx1, outy1, outx0, outy0, x_column, box_y, jump_sig, load_colour, enable, CLOCK_50, resetn, draw_dino, draw0, draw1, draw2, draw3, draw_erase, dinowriteEn, boxwriteEn, writeEn, x, y, colour);
		
		box_control bc0(clock4out, box_en, column_erase_en, dinowriteEn, CLOCK_50, resetn, draw_dino, draw0, draw1, draw2, draw3, draw_erase, boxwriteEn);
		boxes_tracker b0(new_y_en, generated_y, clock4out, resetn, outx3, outy3, outx2, outy2, outx1, outy1, outx0, outy0, box_en, erase_box, box_y);
		
		random_height rh0(CLOCK_50, clock4out, resetn, 1'b1, generated_y, new_y_en);
		
		dino_control c(enable, CLOCK_50, resetn, key, load_colour, dinowriteEn); 

		assign jump_sig = 1'b1;
		
	
	endmodule

	module hexdecoder(hex_digit, segments);
    input [3:0] hex_digit;
    output reg [6:0] segments;
   
    always @(*)
        case (hex_digit)
            4'h0: segments = 7'b100_0000;
            4'h1: segments = 7'b111_1001;
            4'h2: segments = 7'b010_0100;
            4'h3: segments = 7'b011_0000;
            4'h4: segments = 7'b001_1001;
            4'h5: segments = 7'b001_0010;
            4'h6: segments = 7'b000_0010;
            4'h7: segments = 7'b111_1000;
            4'h8: segments = 7'b000_0000;
            4'h9: segments = 7'b001_1000;
            4'hA: segments = 7'b000_1000;
            4'hB: segments = 7'b000_0011;
            4'hC: segments = 7'b100_0110;
            4'hD: segments = 7'b010_0001;
            4'hE: segments = 7'b000_0110;
            4'hF: segments = 7'b000_1110;   
            default: segments = 7'h7f;
        endcase
	endmodule


# Control Unit Path

	module ControlUnit(enable, clk, reset, out);
	input reset, clk, enable;
	output reg [1:0] out;
	
	always @(posedge clk)
	begin
		if(~reset)
			out <= 2'b0;
		else if (enable)
		begin
			if (out == 2'b11)
				out <= 2'b0;
			else 
				out <= out + 1'b1;
		end
	end
			
	endmodule

	module rate_counter(enable, clk, reset, out);
	input clk, reset, enable;
	output reg [1:0] out;
	
	always @(posedge clk)
	begin
		if (~reset)
			out <= 2'b11;
		else if (enable)
		begin
			if (out == 2'b0)
				out <= 2'b11;
			else
				out <= out - 1'b1;
		end
	end
	endmodule




	module delay_counter(enable, clk, reset, out, q);
	input enable, clk, reset;
	
	// 1/60 second equal to 60hz
	// 50Mhz/60hz = 833333.333
	// ceil(log2(833333.333)) = 20
	output reg [19:0] out;
	output reg q;
	
	always @(posedge clk)
	begin 
		if(~reset) begin
			out <= 20'd0;
			q <= 1'b0;
		end
		else if (enable)
		begin
			if (out == 20'b0) begin
				out <= 20'd1666666;
				q <= 1'b1;
			end
			else begin
				out <= out - 1'b1;
				q <= 1'b0;
			end
		end
	end
	endmodule

	module frame_counter(enable, clk, reset, out);
	input enable, clk, reset;
	output reg [3:0] out;
	
	always @(posedge clk)
	begin
		if(~reset)
			out <= 4'b0;
		else if(enable)
		begin
		  if(out == 4'd7)
			  out <= 4'b0;
		  else
			  out <= out + 1'b1;
		end
  	 end
	endmodule


	module jump_counter(enable, clk, reset, jump_sig, out);
	input enable, clk, reset, jump_sig;
	output reg [7:0] out;
	
	always @(negedge clk)
	begin
		if(~reset)
			out <= 8'd110;
		
		else if (enable)
		begin
			if (~jump_sig)
			begin
				 if (out == 8'd40)
					out <= 8'd110;
				 else if(8'd40 < out)
					out <= out - 1'b1;
			end
			else
			begin
				if (out < 8'd111)
					out <= out + 1'b1;
			end
			
		end
		
	end
	endmodule
				
		

	module lab7_part2_datapath (x_in, y_in, c_in, load_colour, clk, reset, enable, x_out, y_out, c_out);
	input clk, enable, load_colour, reset;
	input [2:0] c_in;
	input [7:0] x_in, y_in;
	 
	output [2:0] c_out;
	output [7:0] x_out, y_out;
	
	reg [7:0] x_init, y_init, c_init;
	
	wire [1:0] width_out;
	wire [1:0] rate_out;
	wire [1:0] height_out;
	
	
	always @(posedge clk)
	begin
		if(~reset)
		begin
			x_init <= 8'b0;
			y_init <= 8'b0;
			c_init <= 3'b0;
		end
		else
		begin
			x_init <= x_in;
			y_init <= y_in;
			
			if (load_colour)
				c_init <= c_in;
		end
	end
	
	length_counter wc(.enable(enable), .clk(clk), .reset(reset), .out(width_out));
	
	rate_counter next_row(.enable(enable), .clk(clk), .reset(reset), .out(rate_out));
	
	assign y_enable = (rate_out == 2'b0) ? 1 : 0;
	
	length_counter hc(.enable(y_enable), .clk(clk), .reset(reset), .out(height_out));
	
	assign x_out = x_init + width_out;
	assign y_out = y_init + height_out;
	assign c_out = c_init;
	endmodule
	

	module dino_datapath(enable, clk, load_colour,  reset, jump_sig,  x_out, y_out, c_out);
	input enable, clk, reset, load_colour, jump_sig;

	output [7:0] x_out;
	output reg [6:0] y_out;
	output [2:0] c_out;
	
	wire [19:0] delay_value;
	wire delay_out;
	wire [3:0] frame_out;
	wire [7:0] jump_out;
	wire [2:0] col;
	wire [7:0] y_out_temp;
	
	delay_counter dc(.enable(enable), .clk(clk), .reset(reset), .out(delay_value), .q(delay_out));
	
	assign frame_en = (delay_value == 20'd0) ? 1 : 0;
	
	frame_counter fc(.enable(frame_en), .clk(clk), .reset(reset), .out(frame_out));
	
	assign jump_en = (frame_out == 4'd7) ? 1 : 0;
	//	assign jump_en = (delay_out == 1'b1) ? 1 : 0;
	
	jump_counter jc(
		.clk(jump_en), 
		.enable(enable), 
		.reset(reset), 
		.jump_sig(jump_sig),
		.out(jump_out)
	);
	
	//track_height j_height(.clk(clk), .reset(reset), .y_val(jump_out), .jump_sig(jump_out));
	
	assign col = (frame_out == 4'd7) ? 3'b000 : 3'b010;
	//	assign col = (delay_out == 1'b1) ? 3'b000 : 3'b010;
	
	lab7_part2_datapath d0(
		.x_in(8'd24), 
		.y_in(jump_out), 
		.c_in(col), 
		.load_colour(load_colour), 
		.clk(clk), 
		.reset(reset), 
		.enable(enable), 
		.x_out(x_out), 
		.y_out(y_out_temp), 
		.c_out(c_out)
	);
	
	always @(*)
		y_out = y_out_temp[6:0];
	endmodule

	module dino_control(pause, enable, clk, reset, go, load_colour, plot);
	input clk, reset, go, pause;
	output reg enable, plot, load_colour;
	
	reg [2:0] current_state, next_state;
	
	localparam S_LOAD_DINO = 3'd0,
				  S_LOAD_DINO_WAIT = 3'd1,
				  S_JUMP_DINO = 3'd2,
				  PAUSE_STATE = 3'd3;
				  
				  
	always @(*)
	begin: state_table
		case (current_state)
			S_LOAD_DINO: next_state = go ? S_LOAD_DINO_WAIT : S_LOAD_DINO;
			S_LOAD_DINO_WAIT: next_state = go ? S_JUMP_DINO : S_LOAD_DINO_WAIT;
			S_JUMP_DINO: next_state = pause ? PAUSE_STATE : S_JUMP_DINO;
			PAUSE_STATE: next_state = pause ? PAUSE_STATE: S_JUMP_DINO;
		default: next_state = S_LOAD_DINO;
		endcase
	end
	
	always @(*)
	begin: enable_signals

		enable = 1'b0;
		load_colour = 1'b0;
		plot = 1'b0;
		
		case(current_state)
			S_JUMP_DINO: begin
				load_colour = 1'b1;
				enable = 1'b1;
				plot = 1'b1;
			end
			PAUSE_STATE: begin
				enable = 1'b0;
			end
		endcase
	end
	
	always @(posedge clk)
	begin: state_FFs
		if(~reset)
			current_state <= S_LOAD_DINO;
		else
			current_state <= next_state;
	end
	endmodule

# VGA Setup 
Since we need to use lcd on the fpga

	module vga_adapter(
			resetn,
			clock,
			colour,
			x, y, plot,
			/* Signals for the DAC to drive the monitor. */
			VGA_R,
			VGA_G,
			VGA_B,
			VGA_HS,
			VGA_VS,
			VGA_BLANK,
			VGA_SYNC,
			VGA_CLK);
 
	parameter BITS_PER_COLOUR_CHANNEL = 1;
	/* The number of bits per colour channel used to represent the colour of each pixel. A value
	 * of 1 means that Red, Green and Blue colour channels will use 1 bit each to represent the intensity
	 * of the respective colour channel. For BITS_PER_COLOUR_CHANNEL=1, the adapter can display 8 colours.
	 * In general, the adapter is able to use 2^(3*BITS_PER_COLOUR_CHANNEL ) colours. The number of colours is
	 * limited by the screen resolution and the amount of on-chip memory available on the target device.
	 */	
	
	parameter MONOCHROME = "FALSE";
	/* Set this parameter to "TRUE" if you only wish to use black and white colours. Doing so will reduce
	 * the amount of memory you will use by a factor of 3. */
	
	parameter RESOLUTION = "320x240";
	/* Set this parameter to "160x120" or "320x240". It will cause the VGA adapter to draw each dot on
	 * the screen by using a block of 4x4 pixels ("160x120" resolution) or 2x2 pixels ("320x240" resolution).
	 * It effectively reduces the screen resolution to an integer fraction of 640x480. It was necessary
	 * to reduce the resolution for the Video Memory to fit within the on-chip memory limits.
	 */
	
	parameter BACKGROUND_IMAGE = "background.mif";
	/* The initial screen displayed when the circuit is first programmed onto the DE2 board can be
	 * defined useing an MIF file. The file contains the initial colour for each pixel on the screen
	 * and is placed in the Video Memory (VideoMemory module) upon programming. Note that resetting the
	 * VGA Adapter will not cause the Video Memory to revert to the specified image. */


	/*****************************************************************************/
	/* Declare inputs and outputs.                                               */
	/*****************************************************************************/
	input resetn;
	input clock;
	
	/* The colour input can be either 1 bit or 3*BITS_PER_COLOUR_CHANNEL bits wide, depending on
	 * the setting of the MONOCHROME parameter.
	 */
	input [((MONOCHROME == "TRUE") ? (0) : (BITS_PER_COLOUR_CHANNEL*3-1)):0] colour;
	
	/* Specify the number of bits required to represent an (X,Y) coordinate on the screen for
	 * a given resolution.
	 */
	input [((RESOLUTION == "320x240") ? (8) : (7)):0] x; 
	input [((RESOLUTION == "320x240") ? (7) : (6)):0] y;
	
	/* When plot is high then at the next positive edge of the clock the pixel at (x,y) will change to
	 * a new colour, defined by the value of the colour input.
	 */
	input plot;
	
	/* These outputs drive the VGA display. The VGA_CLK is also used to clock the FSM responsible for
	 * controlling the data transferred to the DAC driving the monitor. */
	output [7:0] VGA_R;
	output [7:0] VGA_G;
	output [7:0] VGA_B;
	output VGA_HS;
	output VGA_VS;
	output VGA_BLANK;
	output VGA_SYNC;
	output VGA_CLK;

	/*****************************************************************************/
	/* Declare local signals here.                                               */
	/*****************************************************************************/
	
	wire valid_160x120;
	wire valid_320x240;
	/* Set to 1 if the specified coordinates are in a valid range for a given resolution.*/
	
	wire writeEn;
	/* This is a local signal that allows the Video Memory contents to be changed.
	 * It depends on the screen resolution, the values of X and Y inputs, as well as 
	 * the state of the plot signal.
	 */
	
	wire [((MONOCHROME == "TRUE") ? (0) : (BITS_PER_COLOUR_CHANNEL*3-1)):0] to_ctrl_colour;
	/* Pixel colour read by the VGA controller */
	
	wire [((RESOLUTION == "320x240") ? (16) : (14)):0] user_to_video_memory_addr;
	/* This bus specifies the address in memory the user must write
	 * data to in order for the pixel intended to appear at location (X,Y) to be displayed
	 * at the correct location on the screen.
	 */
	
	wire [((RESOLUTION == "320x240") ? (16) : (14)):0] controller_to_video_memory_addr;
	/* This bus specifies the address in memory the vga controller must read data from
	 * in order to determine the colour of a pixel located at coordinate (X,Y) of the screen.
	 */
	
	wire clock_25;
	/* 25MHz clock generated by dividing the input clock frequency by 2. */
	
	wire vcc, gnd;
	
	/*****************************************************************************/
	/* Instances of modules for the VGA adapter.                                 */
	/*****************************************************************************/	
	assign vcc = 1'b1;
	assign gnd = 1'b0;
	
	vga_address_translator user_input_translator(
					.x(x), .y(y), .mem_address(user_to_video_memory_addr) );
		defparam user_input_translator.RESOLUTION = RESOLUTION;
	/* Convert user coordinates into a memory address. */

	assign valid_160x120 = (({1'b0, x} >= 0) & ({1'b0, x} < 160) & ({1'b0, y} >= 0) & ({1'b0, y} < 120)) & (RESOLUTION == "160x120");
	assign valid_320x240 = (({1'b0, x} >= 0) & ({1'b0, x} < 320) & ({1'b0, y} >= 0) & ({1'b0, y} < 240)) & (RESOLUTION == "320x240");
	assign writeEn = (plot) & (valid_160x120 | valid_320x240);
	/* Allow the user to plot a pixel if and only if the (X,Y) coordinates supplied are in a valid range. */
	
	/* Create video memory. */
	altsyncram	VideoMemory (
				.wren_a (writeEn),
				.wren_b (gnd),
				.clock0 (clock), // write clock
				.clock1 (clock_25), // read clock
				.clocken0 (vcc), // write enable clock
				.clocken1 (vcc), // read enable clock				
				.address_a (user_to_video_memory_addr),
				.address_b (controller_to_video_memory_addr),
				.data_a (colour), // data in
				.q_b (to_ctrl_colour)	// data out
				);
	defparam
		VideoMemory.WIDTH_A = ((MONOCHROME == "FALSE") ? (BITS_PER_COLOUR_CHANNEL*3) : 1),
		VideoMemory.WIDTH_B = ((MONOCHROME == "FALSE") ? (BITS_PER_COLOUR_CHANNEL*3) : 1),
		VideoMemory.INTENDED_DEVICE_FAMILY = "Cyclone II",
		VideoMemory.OPERATION_MODE = "DUAL_PORT",
		VideoMemory.WIDTHAD_A = ((RESOLUTION == "320x240") ? (17) : (15)),
		VideoMemory.NUMWORDS_A = ((RESOLUTION == "320x240") ? (76800) : (19200)),
		VideoMemory.WIDTHAD_B = ((RESOLUTION == "320x240") ? (17) : (15)),
		VideoMemory.NUMWORDS_B = ((RESOLUTION == "320x240") ? (76800) : (19200)),
		VideoMemory.OUTDATA_REG_B = "CLOCK1",
		VideoMemory.ADDRESS_REG_B = "CLOCK1",
		VideoMemory.CLOCK_ENABLE_INPUT_A = "BYPASS",
		VideoMemory.CLOCK_ENABLE_INPUT_B = "BYPASS",
		VideoMemory.CLOCK_ENABLE_OUTPUT_B = "BYPASS",
		VideoMemory.POWER_UP_UNINITIALIZED = "FALSE",
		VideoMemory.INIT_FILE = BACKGROUND_IMAGE;
		
	vga_pll mypll(clock, clock_25);
	/* This module generates a clock with half the frequency of the input clock.
	 * For the VGA adapter to operate correctly the clock signal 'clock' must be
	 * a 50MHz clock. The derived clock, which will then operate at 25MHz, is
	 * required to set the monitor into the 640x480@60Hz display mode (also known as
	 * the VGA mode).
	 */

	wire [9:0] r;
	wire [9:0] g;
	wire [9:0] b;
	
	/* Assign the MSBs from the controller to the VGA signals */
	
	assign VGA_R = r[9:2];
	assign VGA_G = g[9:2];
	assign VGA_B = b[9:2];
	
	vga_controller controller(
			.vga_clock(clock_25),
			.resetn(resetn),
			.pixel_colour(to_ctrl_colour),
			.memory_address(controller_to_video_memory_addr), 
			.VGA_R(r),
			.VGA_G(g),
			.VGA_B(b),
			.VGA_HS(VGA_HS),
			.VGA_VS(VGA_VS),
			.VGA_BLANK(VGA_BLANK),
			.VGA_SYNC(VGA_SYNC),
			.VGA_CLK(VGA_CLK)				
		);
		defparam controller.BITS_PER_COLOUR_CHANNEL  = BITS_PER_COLOUR_CHANNEL ;
		defparam controller.MONOCHROME = MONOCHROME;
		defparam controller.RESOLUTION = RESOLUTION;

	endmodule
	module vga_address_translator(x, y, mem_address);

	parameter RESOLUTION = "320x240";
	/* Set this parameter to "160x120" or "320x240". It will cause the VGA adapter to draw each dot on
	 * the screen by using a block of 4x4 pixels ("160x120" resolution) or 2x2 pixels ("320x240" resolution).
	 * It effectively reduces the screen resolution to an integer fraction of 640x480. It was necessary
	 * to reduce the resolution for the Video Memory to fit within the on-chip memory limits.
	 */

	input [((RESOLUTION == "320x240") ? (8) : (7)):0] x; 
	input [((RESOLUTION == "320x240") ? (7) : (6)):0] y;	
	output reg [((RESOLUTION == "320x240") ? (16) : (14)):0] mem_address;
	
	/* The basic formula is address = y*WIDTH + x;
	 * For 320x240 resolution we can write 320 as (256 + 64). Memory address becomes
	 * (y*256) + (y*64) + x;
	 * This simplifies multiplication a simple shift and add operation.
	 * A leading 0 bit is added to each operand to ensure that they are treated as unsigned
	 * inputs. By default the use a '+' operator will generate a signed adder.
	 * Similarly, for 160x120 resolution we write 160 as 128+32.
	 */
	wire [16:0] res_320x240 = ({1'b0, y, 8'd0} + {1'b0, y, 6'd0} + {1'b0, x});
	wire [15:0] res_160x120 = ({1'b0, y, 7'd0} + {1'b0, y, 5'd0} + {1'b0, x});
	
	always @(*)
	begin
		if (RESOLUTION == "320x240")
			mem_address = res_320x240;
		else
			mem_address = res_160x120[14:0];
	end
	endmodule
	
	module vga_controller(	vga_clock, resetn, pixel_colour, memory_address, 
		VGA_R, VGA_G, VGA_B,
		VGA_HS, VGA_VS, VGA_BLANK,
		VGA_SYNC, VGA_CLK);
	
	/* Screen resolution and colour depth parameters. */
	
	parameter BITS_PER_COLOUR_CHANNEL = 1;
	/* The number of bits per colour channel used to represent the colour of each pixel. A value
	 * of 1 means that Red, Green and Blue colour channels will use 1 bit each to represent the intensity
	 * of the respective colour channel. For BITS_PER_COLOUR_CHANNEL=1, the adapter can display 8 colours.
	 * In general, the adapter is able to use 2^(3*BITS_PER_COLOUR_CHANNEL) colours. The number of colours is
	 * limited by the screen resolution and the amount of on-chip memory available on the target device.
	 */	
	
	parameter MONOCHROME = "FALSE";
	/* Set this parameter to "TRUE" if you only wish to use black and white colours. Doing so will reduce
	 * the amount of memory you will use by a factor of 3. */
	
	parameter RESOLUTION = "320x240";
	/* Set this parameter to "160x120" or "320x240". It will cause the VGA adapter to draw each dot on
	 * the screen by using a block of 4x4 pixels ("160x120" resolution) or 2x2 pixels ("320x240" resolution).
	 * It effectively reduces the screen resolution to an integer fraction of 640x480. It was necessary
	 * to reduce the resolution for the Video Memory to fit within the on-chip memory limits.
	 */
	
	//--- Timing parameters.
	/* Recall that the VGA specification requires a few more rows and columns are drawn
	 * when refreshing the screen than are actually present on the screen. This is necessary to
	 * generate the vertical and the horizontal syncronization signals. If you wish to use a
	 * display mode other than 640x480 you will need to modify the parameters below as well
	 * as change the frequency of the clock driving the monitor (VGA_CLK).
	 */
	parameter C_VERT_NUM_PIXELS  = 10'd480;
	parameter C_VERT_SYNC_START  = 10'd493;
	parameter C_VERT_SYNC_END    = 10'd494; //(C_VERT_SYNC_START + 2 - 1); 
	parameter C_VERT_TOTAL_COUNT = 10'd525;

	parameter C_HORZ_NUM_PIXELS  = 10'd640;
	parameter C_HORZ_SYNC_START  = 10'd659;
	parameter C_HORZ_SYNC_END    = 10'd754; //(C_HORZ_SYNC_START + 96 - 1); 
	parameter C_HORZ_TOTAL_COUNT = 10'd800;	
		
	/*****************************************************************************/
	/* Declare inputs and outputs.                                               */
	/*****************************************************************************/
	
	input vga_clock, resetn;
	input [((MONOCHROME == "TRUE") ? (0) : (BITS_PER_COLOUR_CHANNEL*3-1)):0] pixel_colour;
	output [((RESOLUTION == "320x240") ? (16) : (14)):0] memory_address;
	output reg [9:0] VGA_R;
	output reg [9:0] VGA_G;
	output reg [9:0] VGA_B;
	output reg VGA_HS;
	output reg VGA_VS;
	output reg VGA_BLANK;
	output VGA_SYNC, VGA_CLK;
	
	/*****************************************************************************/
	/* Local Signals.                                                            */
	/*****************************************************************************/
	
	reg VGA_HS1;
	reg VGA_VS1;
	reg VGA_BLANK1; 
	reg [9:0] xCounter, yCounter;
	wire xCounter_clear;
	wire yCounter_clear;
	wire vcc;
	
	reg [((RESOLUTION == "320x240") ? (8) : (7)):0] x; 
	reg [((RESOLUTION == "320x240") ? (7) : (6)):0] y;	
	/* Inputs to the converter. */
	
	/*****************************************************************************/
	/* Controller implementation.                                                */
	/*****************************************************************************/

	assign vcc =1'b1;
	
	/* A counter to scan through a horizontal line. */
	always @(posedge vga_clock or negedge resetn)
	begin
		if (!resetn)
			xCounter <= 10'd0;
		else if (xCounter_clear)
			xCounter <= 10'd0;
		else
		begin
			xCounter <= xCounter + 1'b1;
		end
	end
	assign xCounter_clear = (xCounter == (C_HORZ_TOTAL_COUNT-1));

	/* A counter to scan vertically, indicating the row currently being drawn. */
	always @(posedge vga_clock or negedge resetn)
	begin
		if (!resetn)
			yCounter <= 10'd0;
		else if (xCounter_clear && yCounter_clear)
			yCounter <= 10'd0;
		else if (xCounter_clear)		//Increment when x counter resets
			yCounter <= yCounter + 1'b1;
	end
	assign yCounter_clear = (yCounter == (C_VERT_TOTAL_COUNT-1)); 
	
	/* Convert the xCounter/yCounter location from screen pixels (640x480) to our
	 * local dots (320x240 or 160x120). Here we effectively divide x/y coordinate by 2 or 4,
	 * depending on the resolution. */
	always @(*)
	begin
		if (RESOLUTION == "320x240")
		begin
			x = xCounter[9:1];
			y = yCounter[8:1];
		end
		else
		begin
			x = xCounter[9:2];
			y = yCounter[8:2];
		end
	end
	
	/* Change the (x,y) coordinate into a memory address. */
	vga_address_translator controller_translator(
					.x(x), .y(y), .mem_address(memory_address) );
		defparam controller_translator.RESOLUTION = RESOLUTION;


	/* Generate the vertical and horizontal synchronization pulses. */
	always @(posedge vga_clock)
	begin
		//- Sync Generator (ACTIVE LOW)
		VGA_HS1 <= ~((xCounter >= C_HORZ_SYNC_START) && (xCounter <= C_HORZ_SYNC_END));
		VGA_VS1 <= ~((yCounter >= C_VERT_SYNC_START) && (yCounter <= C_VERT_SYNC_END));
		
		//- Current X and Y is valid pixel range
		VGA_BLANK1 <= ((xCounter < C_HORZ_NUM_PIXELS) && (yCounter < C_VERT_NUM_PIXELS));	
	
		//- Add 1 cycle delay
		VGA_HS <= VGA_HS1;
		VGA_VS <= VGA_VS1;
		VGA_BLANK <= VGA_BLANK1;	
	end
	
	/* VGA sync should be 1 at all times. */
	assign VGA_SYNC = vcc;
	
	/* Generate the VGA clock signal. */
	assign VGA_CLK = vga_clock;
	
	/* Brighten the colour output. */
	// The colour input is first processed to brighten the image a little. Setting the top
	// bits to correspond to the R,G,B colour makes the image a bit dull. To brighten the image,
	// each bit of the colour is replicated through the 10 DAC colour input bits. For example,
	// when BITS_PER_COLOUR_CHANNEL is 2 and the red component is set to 2'b10, then the
	// VGA_R input to the DAC will be set to 10'b1010101010.
	
	integer index;
	integer sub_index;
	
	always @(pixel_colour)
	begin		
		VGA_R <= 'b0;
		VGA_G <= 'b0;
		VGA_B <= 'b0;
		if (MONOCHROME == "FALSE")
		begin
			for (index = 10-BITS_PER_COLOUR_CHANNEL; index >= 0; index = index - BITS_PER_COLOUR_CHANNEL)
			begin
				for (sub_index = BITS_PER_COLOUR_CHANNEL - 1; sub_index >= 0; sub_index = sub_index - 1)
				begin
					VGA_R[sub_index+index] <= pixel_colour[sub_index + BITS_PER_COLOUR_CHANNEL*2];
					VGA_G[sub_index+index] <= pixel_colour[sub_index + BITS_PER_COLOUR_CHANNEL];
					VGA_B[sub_index+index] <= pixel_colour[sub_index];
				end
			end	
		end
		else
		begin
			for (index = 0; index < 10; index = index + 1)
			begin
				VGA_R[index] <= pixel_colour[0:0];
				VGA_G[index] <= pixel_colour[0:0];
				VGA_B[index] <= pixel_colour[0:0];
			end	
		end
	end

	endmodule
	`timescale 1 ps / 1 ps
	// synopsys translate_on
	module vga_pll (
	clock_in,
	clock_out);

	input	  clock_in;
	output	  clock_out;

	wire [5:0] clock_output_bus;
	wire [1:0] clock_input_bus;
	wire gnd;
	
	assign gnd = 1'b0;
	assign clock_input_bus = { gnd, clock_in }; 

	altpll	altpll_component (
				.inclk (clock_input_bus),
				.clk (clock_output_bus)
				);
	defparam
		altpll_component.operation_mode = "NORMAL",
		altpll_component.intended_device_family = "Cyclone II",
		altpll_component.lpm_type = "altpll",
		altpll_component.pll_type = "FAST",
		/* Specify the input clock to be a 50MHz clock. A 50 MHz clock is present
		 * on PIN_N2 on the DE2 board. We need to specify the input clock frequency
		 * in order to set up the PLL correctly. To do this we must put the input clock
		 * period measured in picoseconds in the inclk0_input_frequency parameter.
		 * 1/(20000 ps) = 0.5 * 10^(5) Hz = 50 * 10^(6) Hz = 50 MHz. */
		altpll_component.inclk0_input_frequency = 20000,
		altpll_component.primary_clock = "INCLK0",
		/* Specify output clock parameters. The output clock should have a
		 * frequency of 25 MHz, with 50% duty cycle. */
		altpll_component.compensate_clock = "CLK0",
		altpll_component.clk0_phase_shift = "0",
		altpll_component.clk0_divide_by = 2,
		altpll_component.clk0_multiply_by = 1,		
		altpll_component.clk0_duty_cycle = 50;
		
	assign clock_out = clock_output_bus[0];

	endmodule
	
