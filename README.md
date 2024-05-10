# Project Description

The program is designed to monitor directories, create snapshots of directory contents, and perform syntactic analysis on potentially corrupted files within those directories.

# Overview Of Functionalities:

## *Directory Monitoring:*

The program accepts multiple directory paths as command-line arguments. The user can specify the directory to be monitored as an argument in the command line, and the program will track changes occurring in it and its subdirectories, parsing recursively each entry from the directory.
Each directory is monitored independently using child processes, allowing for parallel processing of multiple directories.

## *Snapshot Creation:*

For each monitored directory, the program creates a snapshot file.
The snapshot file contains detailed information about each file and sub-directory within the monitored directory.
Information includes `file paths`, `sizes`, `access rights`, and the number of `hard links`.

## *Snapshot Comparison:*

After creating a snapshot, the program compares it with the previous snapshot (if available) for the same directory.
If differences are found between the current and previous snapshots, it `overrides` the previous snapshot.
Differences indicate changes in file structure or attributes within the monitored directory.

## *Syntactic Analysis:*

The program identifies files with `missing access permissions`, indicating potential corruption or security threats.
Files lacking all access permissions are subjected to `syntactic analysis` using an external script (`verify_for_malicious.sh`).
Files are considered `suspect` if `no_line < 3 && no_words > 999 && no_characters > 1999`.
The analysis is performed in a separate child process to prevent blocking the main execution flow.

## *Isolation of Corrupted Files:*

If the syntactic analysis detects a potentially corrupted or malicious file , it is moved to an isolated directory only if it contains `non-ascii characters` or keywords like: `corrupted`, `risk`, `malicious`, `attack`.
This ensures that such files are segregated from the rest of the system to prevent further harm.

## *Process Management:*

The program manages multiple processes efficiently using `fork()` and `wait()` system calls.
Child processes are responsible for monitoring individual directories, creating snapshots, and performing syntactic analysis.
In certain cases, child processes create grandchild processes to perform syntactic analysis on files.
The main process coordinates the overall execution and waits for child processes to complete.

## *Inter-Process Communication*

Pipes facilitate communication between child and grandchild processes.
They enable the `transmission of data`, such as `analysis results`, between processes.
Pipes ensure synchronization and coordination, allowing for orderly execution and proper handling of process dependencies.

## *Error Handling:*

The program includes error handling mechanisms to address various scenarios, such as incorrect directory paths, failed file operations,failed allocation of memory or script execution errors.
Error messages are printed to `standard error (stderr)` to facilitate debugging and troubleshooting.

# Project Testing

I tested my implementation on my `Fedora` operating system. The only problem I encountered when I tested the algorrithm was when I monitored the same directory more than once (because the processes are running in parallel I encountered a problem with the comparation of snapshots). 

# Additional Project Information

## *Files:*

The project contains the `final_build.c`, the executable of the project, `run_final_build`, the script for the syntactic analysis, `verify_for_malicious.sh`, this `README.md` file and all the old versions of the project that can be fount in the `old_versions` directory.

## *Running The Project:*

The project can be compiled using `gcc -o run_final_build final_build.c`
After compiling, the project can be runned using `./run_final_build -o Output_dir -s Isolated_dir Dir_1 Dir_ 2 Dir_3 ... ` (NOTE: `-o Output_dir` ,`-s Isolated_dir` and the directories that are going to be monitored can be placed in any order. `e.g. ./run_final_build Dir_1 -s Isolated_dir Dir_2 -o Output_dir Dir_3`)


