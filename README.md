# SIMD Co-Processor in Verilog HDL

## 👨‍💼 Developer

**Name:** Adarsh Prakash
**Role:** Design Verification Intern
**📧 Email:** [kumaradarsh663@gmail.com](mailto:kumaradarsh663@gmail.com)

---

## 📌 Overview

This project implements a **SIMD (Single Instruction, Multiple Data) Co-Processor** using Verilog HDL. It performs **parallel addition** of two 4-element vectors, with each element being 8 bits wide. This design serves as a fundamental building block for parallel processors used in GPUs and AI accelerators.

---

## 🧐 Features

* **Parametrized Design**: Configurable bit width (`WIDTH`) and number of elements (`N`)
* **SIMD Operation**: Supports parallel addition of vectors
* **Verification**: Includes a testbench for functional verification
* **Waveform Analysis**: Generates VCD dump for waveform inspection

---

## 📁 Files

| File        | Description                                       |
| ----------- | ------------------------------------------------- |
| `simd.v`    | Verilog RTL design for the SIMD Co-Processor      |
| `tb_simd.v` | Verilog testbench for simulation and verification |
| `README.md` | Documentation and usage guide                     |

---

## 🧪 Test Case

**Inputs:**

* `A = [1, 2, 3, 4]`
* `B = [10, 20, 30, 40]`

**Expected Output:**

* `Result = [11, 22, 33, 44]`
* **Hex Representation:** `2C21160B`

---

## 🔧 Run Commands

**Compile (VCS):**

```bash
vcs simd.v tb_simd.v -lca -kdb -debug_access+all -full64
```

**Simulate:**

```bash
./simv
```

**Waveform Viewing (Verdi):**

```bash
vcs -full64 simd.v tb_simd.v -debug_access+all -lca -kdb
./simv -verdi
```

---

## 📜 Code

### `simd.v`

```systemverilog
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
```

---

### `tb_simd.v`

```systemverilog
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
```

---

## 🖼️ Screenshots

### Simulation Output

![Screenshot 2025-06-07 at 5 48 28 AM](https://github.com/user-attachments/assets/faf624e7-7877-4593-899b-e5cfb4b3b555)

### Terminal Output with Compilation & Execution

![Screenshot 2025-06-07 at 5 48 44 AM](https://github.com/user-attachments/assets/9ff1c595-14f2-43d9-9d50-4c958975449e)

### Verdi Waveform Dump

![Screenshot 2025-06-07 at 5 49 00 AM](https://github.com/user-attachments/assets/c237a7af-dace-4a93-b009-e94089596675)

<img width="1282" alt="Screenshot 2025-06-07 at 5 52 10 AM" src="https://github.com/user-attachments/assets/f1ed6949-18c8-4ca2-b1f0-7ee04bcf6e28" />

<img width="1357" alt="Screenshot 2025-06-07 at 5 52 30 AM" src="https://github.com/user-attachments/assets/1355e6b5-151c-41c6-8be7-69461c6cc9ae" />


---

## ⭐ Closing

Feel free to **fork**, **star**, and open **issues** or **suggestions**!
For any inquiries, contact **Adarsh Prakash** at 📧 *[kumaradarsh663@gmail.com](mailto:kumaradarsh663@gmail.com)*
