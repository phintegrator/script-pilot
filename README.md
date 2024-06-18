# Script Pilot

**Script Pilot** is a Python-based FastAPI application designed to manage the execution of multiple Python scripts based on their status defined in a text file named `sp_on_board.txt`. It provides functionalities to start, stop, and monitor these scripts, ensuring that scripts are not duplicated and managing their lifecycle efficiently.

## Installation

To install Script Pilot, follow these steps:

1. Clone the repository:

```bash
git clone https://github.com/phintegrator/script-pilot.git
```

1. Navigate to the project directory:

```bash
cd script-pilot
```

1. Install the required dependencies:

```bash
pip install -r requirements.txt
```

## Key Features

1. **Start Scripts**: Initiates a script if it is not already running and updates its status in `sp_on_board.txt`.
2. **Stop Scripts**: Terminates a running script by PID and updates its status in `sp_on_board.txt`.
3. **Monitor Scripts**: Checks the status and PID of running scripts, reporting if they are running, stopped, or terminated.
4. **Update Scripts**: Reads the `sp_on_board.txt` file to start or stop scripts based on the defined statuses.
5. **Remove Scripts**: Allows the removal of script entries from `sp_on_board.txt` only if they are not running.
6. **Health Check**: Provides a health check endpoint to confirm the service is running correctly.

Paths of scripts are normalized to avoid duplication, ensuring consistent management of script lifecycles.

## Configuration

The application can be configured via a `sp_config.json` file, which should be present in the same directory as the `psm2_web.py` file. If the file is not present, it will be created with the following default configuration:

```json
{
    "max_concurrent_scripts": 5,
    "log_file": "script_pilot.log",
    "log_max_bytes": 1073741824,  # 1 GB
    "port": 8000,
    "username": "admin",
    "password": "password"
}
```

- `max_concurrent_scripts`: The maximum number of scripts that can run concurrently (default: 5).
- `log_file`: The name of the log file (default: `script_pilot.log`).
- `log_max_bytes`: The maximum size of the log file in bytes before rotation (default: 1 GB).
- `port`: The port on which the FastAPI application will run (default: 8000).
- `username`: The username required for authentication (default: `admin`).
- `password`: The password required for authentication (default: `password`).

## Target Scripts File

The `sp_on_board.txt` file is used to define the scripts that should be managed by the Script Pilot application. Each line in the file should have the following format:

```
<script_name> <status> [arguments]
```

- `<script_name>`: The name or path of the Python script file.
- `<status>`: The desired status of the script, which can be either `run` or `stop`.
- `[arguments]`: Optional arguments to be passed to the script when running (space-separated).

Example:

```
my_script.py run
another_script.py stop --arg1 --arg2
```

## API Endpoints

The Script Pilot application provides the following API endpoints:

### `/start_script/` (POST)

**Summary**: Start a script.

**Description**: Start a specified script if it's not already running.

**Request Body**:

- `script_name` (str): The name or path of the script to start.
- `arguments` (str, optional): Arguments to pass to the script.

**Response**:

- `message` (str): A message indicating the status of the operation.
- `path` (str): The path of the started script.

### `/stop_script/` (POST)

**Summary**: Stop a script.

**Description**: Stop a running script using its PID.

**Request Body**:

- `pid` (int): The process ID (PID) of the running script.

**Response**:

- `message` (str): A message indicating the status of the operation.

### `/monitor_script/` (GET)

**Summary**: Monitor scripts.

**Description**: Get the status of all scripts.

**Response**:

- A dictionary containing the status information for each script, with the script name as the key and a `ScriptStatus` object as the value.

### `/monitor_script/{script_name}` (GET)

**Summary**: Monitor a specific script.

**Description**: Get the status of a specific script.

**Path Parameters**:

- `script_name` (str): The name or path of the script to monitor.

**Response**:

- A `ScriptStatus` object containing the status information for the specified script.

### `/update_scripts/` (POST)

**Summary**: Update scripts.

**Description**: Update the statuses of all scripts based on `sp_on_board.txt`.

**Response**:

- `message` (str): A message indicating the status of the operation.

### `/remove_script/{script_name}` (DELETE)

**Summary**: Remove a script.

**Description**: Remove a script entry from `sp_on_board.txt` if it's not running.

**Path Parameters**:

- `script_name` (str): The name or path of the script to remove.

**Response**:

- `message` (str): A message indicating the status of the operation.

### `/health` (GET)

**Summary**: Health check.

**Description**: Check the health of the ScriptPilot API.

**Response**:

- `status` (str): The health status of the application.
- `script_name` (str): The name of the Script Pilot application.
- `script_version` (str): The version of the Script Pilot application.
- `script_details` (str): Detailed information about the Script Pilot application.

## Authentication

All API endpoints, except for the `/health` endpoint, require authentication. The application uses HTTP Basic Authentication, and the credentials are specified in the `sp_config.json` file (`username` and `password` fields).

## Logging

The application uses a rotating file handler for logging, with the log file name and maximum size specified in the `sp_config.json` file (`log_file` and `log_max_bytes` fields, respectively). The log file will have a maximum of 5 backup files, and the log level is set to `INFO`.

## Running the Application

To run the Script Pilot application, execute the `psm2_web.py` file:

```
python script_pilot.py
```

By default, the application will run on `http://localhost:8000`. You can access the Swagger UI documentation at `http://localhost:8000/docs`.

Note that the application normalizes the paths of the scripts to avoid duplication and ensures consistent management of script lifecycles.

## **License**

This project is licensed under the MIT License - see the LICENSE file for details.

## **Contributing**

Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.

## **Author**

[Javer Valino](https://github.com/phintegrator)