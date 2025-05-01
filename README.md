# Header_Updater

**Automatically keep your C header files synchronized with function prototypes from your source code.**

Header_Updater is a simple tool consisting of Bash and Python scripts designed to automatically scan your C source files (`.c`) and update a specified header file (`.h`) with the corresponding function prototypes. It monitors your project directory for changes and updates the header file in real-time, saving you manual effort and reducing potential errors.

## Features

*   **Automatic Prototype Generation:** Extracts function prototypes from `.c` files.
*   **Header File Synchronization:** Updates a designated header file with the extracted prototypes.
*   **Real-time Monitoring:** Uses `inotify-tools` to watch for changes in `.c` files within the specified project directory.
*   **Background Operation:** Runs silently in the background after being started.
*   **Simple Control:** Easy start and stop scripts (`start.sh`, `stop.sh`).

## Prerequisites

*   **Linux System:** Relies on Linux-specific tools.
*   **Bash:** Required to run the control scripts.
*   **Python 3:** The core logic for parsing and updating is written in Python.
*   **`inotify-tools`:** Used for monitoring file system events.

## Installation

You only need to install `inotify-tools`. Choose the command appropriate for your Linux distribution:

*   **Debian/Ubuntu:**
    ```bash
    sudo apt-get update
    sudo apt-get install inotify-tools
    ```
*   **Fedora/CentOS/RHEL:**
    ```bash
    sudo dnf install inotify-tools
    # Or if using older versions:
    # sudo yum install inotify-tools
    ```

The Python script likely uses standard libraries.

## Usage

### Starting the Monitor

To start monitoring your project and updating the header file, run the `start.sh` script with two arguments:

1.  **Project Directory:** The path (absolute or relative) to the root directory of your C project.
2.  **Header File Path:** The path to the header file you want to update, **relative to the project directory**.

**Syntax:**

```bash
sh start.sh <project_directory> <relative_header_path>
```

**Example:**
Imagine your project structure is:
```bash
/home/user/projects/my_c_project/
├── src/
│   ├── main.c
│   └── utils.c
├── include/
│   └── my_project.h  <-- Header to update
└── start.sh
└── stop.sh
└── (python script)
```

To monitor /home/user/projects/my_c_project/ and update /home/user/projects/my_c_project/include/my_project.h, you would run:
# Using absolute path for project directory
        sh start.sh /home/user/projects/my_c_project/ include/my_project.h

# Using relative path (if you are currently in /home/user/projects/)
        cd /home/user/projects/
        sh start.sh ./my_c_project/ include/my_project.h

# Using home directory shortcut
        sh start.sh ~/projects/my_c_project/ include/my_project.h

The script will now run in the background. Any time a .c file within the specified project directory (and its subdirectories) is saved or modified, the script will re-scan the .c files and update the function prototypes in the specified header file.
Stopping the Monitor
To stop the background monitoring process(es), simply run:
sh stop.sh

This command will find and terminate all instances of the monitoring process started by start.sh.
How it Works (Conceptual)
* start.sh:
  - Takes the project directory and relative header path as arguments.
  - Launches a background process using inotifywait (from inotify-tools) to monitor create, modify, and potentially delete events for .c files within the project directory.
  - When an event occurs, inotifywait triggers the Python script.
  - The Python script receives the project directory and header file path.
* Python Script:
  - Recursively finds all .c files within the project directory.
  - Parses each .c file to identify function definitions (likely using regular expressions or basic parsing).
  - Extracts or generates the standard prototype for each non-static function found.
  - Opens the specified header file (.h).
  - Updates the header file with the generated prototypes. (Note: Understand how this update happens - does it overwrite, replace a section, or append?)
* stop.sh:
  - Identifies the background process(es) associated with the monitor.
  - Terminates the identified process(es) using kill or pkill.
# Important Notes & Caveats
Back up your header file or use version control (like Git) before using this tool.

Performance: For very large projects with frequent changes, the overhead of constant rescanning might become noticeable, although inotify itself is efficient.

Beware: this project works only if you use 42norm.

Currently this project is not finished, currently working on better error and edge cases handling.
