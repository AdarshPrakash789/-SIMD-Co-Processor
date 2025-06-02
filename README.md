<img width="963" alt="Screenshot 2025-06-02 at 10 04 07 PM" src="https://github.com/user-attachments/assets/9dc6fb7d-0928-46cc-bc71-afe654f27df9" />///////////////////////////////////////////
// File: simd.v
// Description: SIMD Co-Processor in Verilog
///////////////////////////////////////////

`timescale 1ns/1ps
module SIMD #(parameter WIDTH = 8, parameter N = 4)(
    input clk,
    input rst,
    input start,
    input [WIDTH*N-1:0] A_flat,
    input [WIDTH*N-1:0] B_flat,
    output reg [WIDTH*N-1:0] result_flat,
    output reg done
);
    integer i;
    reg [WIDTH-1:0] A [0:N-1];
    reg [WIDTH-1:0] B [0:N-1];
    reg [WIDTH-1:0] result [0:N-1];

    // Unpack inputs
    always @(*) begin
        for (i = 0; i < N; i = i + 1) begin
            A[i] = A_flat[i*WIDTH +: WIDTH];
            B[i] = B_flat[i*WIDTH +: WIDTH];
        end
    end

    // SIMD Addition Operation
    always @(posedge clk) begin
        if (rst) begin
            done <= 0;
            result_flat <= 0;
        end else if (start) begin
            for (i = 0; i < N; i = i + 1)
                result[i] <= A[i] + B[i];
            done <= 1;
        end
    end

    // Pack result
    always @(*) begin
        for (i = 0; i < N; i = i + 1)
            result_flat[i*WIDTH +: WIDTH] = result[i];
    end
endmodule


///////////////////////////////////////////
// File: tb_simd.v
// Description: Testbench for SIMD Module
///////////////////////////////////////////

`timescale 1ns/1ps
module tb_simd;
    parameter WIDTH = 8;
    parameter N = 4;

    reg clk, rst, start;
    reg [WIDTH*N-1:0] A_flat, B_flat;
    wire [WIDTH*N-1:0] result_flat;
    wire done;

    // Instantiate the SIMD module
    SIMD #(WIDTH, N) dut (
        .clk(clk),
        .rst(rst),
        .start(start),
        .A_flat(A_flat),
        .B_flat(B_flat),
        .result_flat(result_flat),
        .done(done)
    );

    // Clock generation
    always #5 clk = ~clk;

    initial begin
        $display("SIMD Co-Processor Test Start");
        $dumpfile("simd.vcd");
        $dumpvars(0, tb_simd);

        clk = 0;
        rst = 1;
        start = 0;
        A_flat = 0;
        B_flat = 0;

        #10 rst = 0;
        // A = [1,2,3,4], B = [10,20,30,40]
        A_flat = {8'd4, 8'd3, 8'd2, 8'd1};
        B_flat = {8'd40, 8'd30, 8'd20, 8'd10};

        #10 start = 1;
        #10 start = 0;

        #30;
        $display("Result: %h", result_flat); // Expected: 2C21160B
        $finish;
    end
endmodule


///////////////////////////////////////////
// File: README.md
// Description: GitHub documentation
///////////////////////////////////////////

# SIMD Co-Processor in Verilog HDL

## 👨‍💻 Developer
**Adarsh Prakash**  
**Role**: Design Verification Intern

## 📌 Overview
This project implements a SIMD (Single Instruction, Multiple Data) Co-Processor using Verilog HDL. It adds two 4-element vectors in parallel.

## 📁 Files
- `simd.v` — Verilog RTL design
- `tb_simd.v` — Testbench
- `README.md` — Documentation

## 🔧 Run Commands

### VCS Compile:
```bash
vcs simd.v tb_simd.v -lca -kdb -debug_access+all -full64
```

### Simulation:
```bash
./simv
```

### Waveform Viewing (Verdi):
```bash
vcs -full64 simd.v tb_simd.v -debug_access+all -lca -kdb
./simv -verdi
```

## Output
**Input**: A = [1,2,3,4], B = [10,20,30,40]  
**Expected Output (Hex)**: `2C21160B`
 <img width="871" alt="Screenshot 2025-06-02 at 10 03 21 PM" src="https://github.com/user-attachments/assets/c04e52b9-853f-48ea-8032-95f93b9cb3a2" />

 <img width="966" alt="Screenshot 2025-06-02 at 10 04 23 PM" src="https://github.com/user-attachments/assets/ba58dcda-cac4-4d1d-925c-53243aa73401" />

 <img width="959" alt="Screenshot 2025-06-02 at 10 04 45 PM" src="https://github.com/user-attachments/assets/7d5cbb10-04d7-4d8f-b31b-17f80c680152" />

 <img width="956" alt="Screenshot 2025-06-02 at 10 05 06 PM" src="https://github.com/user-attachments/assets/b44ac8d2-d368-4bd5-abf4-0e9c9a5e3b7e" />

 <img width="1005" alt="Screenshot 2025-06-02 at 10 05 30 PM" src="https://github.com/user-attachments/assets/195bb28e-d57e-4f11-bf2e-44e2391924a7" />

## 📧 Contact
**Adarsh Prakash** 

📧 kumaradarsh663@gmail.com  

