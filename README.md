///////////////////////////////////////////
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
    always @(posedge clk or posedge rst) begin
        if (rst) begin
            done <= 0;
            for (i = 0; i < N; i = i + 1)
                result[i] <= 0;
        end else if (start) begin
            for (i = 0; i < N; i = i + 1)
                result[i] <= A[i] + B[i];
            done <= 1;
        end else begin
            done <= 0; // Reset done if not starting
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

        #20;
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
This project implements a SIMD (Single Instruction, Multiple Data) Co-Processor using Verilog HDL. It performs parallel addition of two 4-element vectors. Each element is 8 bits wide. This design simulates a fundamental building block of parallel processors used in GPUs and AI accelerators.

## 🧠 Features
- Parametrized design: Supports configurable bit width (`WIDTH`) and number of elements (`N`).
- SIMD-based parallel addition.
- Testbench for verification.
- Generates VCD dump for waveform analysis.

## 📁 Files
- `simd.v` — Verilog RTL design for SIMD Co-Processor
- `tb_simd.v` — Verilog testbench file
- `README.md` — Documentation and usage guide

## 🧪 Test Case
**Inputs:**
- `A = [1, 2, 3, 4]`
- `B = [10, 20, 30, 40]`

**Expected Output:**
- `Result = [11, 22, 33, 44]`
- **Hex Representation**: `2C21160B`

## 🔧 Run Commands

### ➤ Compile (VCS):
```bash
vcs simd.v tb_simd.v -lca -kdb -debug_access+all -full64
```

### ➤ Simulate:
```bash
./simv
```

### ➤ Waveform Viewing (Verdi):
```bash
vcs -full64 simd.v tb_simd.v -debug_access+all -lca -kdb
./simv -verdi
```

## 📸 Screenshots

| Simulation Output | Verdi Waveform |
|-------------------|----------------|
| ![Screenshot 1](https://github.com/user-attachments/assets/9dc6fb7d-0928-46cc-bc71-afe654f27df9) | ![Screenshot 4](https://github.com/user-attachments/assets/7d5cbb10-04d7-4d8f-b31b-17f80c680152) |
| ![Screenshot 2](https://github.com/user-attachments/assets/c04e52b9-853f-48ea-8032-95f93b9cb3a2) | ![Screenshot 5](https://github.com/user-attachments/assets/b44ac8d2-d368-4bd5-abf4-0e9c9a5e3b7e) |
| ![Screenshot 3](https://github.com/user-attachments/assets/ba58dcda-cac4-4d1d-925c-53243aa73401) | ![Screenshot 6](https://github.com/user-attachments/assets/195bb28e-d57e-4f11-bf2e-44e2391924a7) |

## 📧 Contact
**Adarsh Prakash**  
📧 kumaradarsh663@gmail.com

---
⭐ Feel free to fork, star and open issues or suggestions!
