#!/usr/bin/env bash

set -e

AVAILABLE_MODES=(output window region)

function Help() {
    cat <<EOF
Usage: hyprshot [options ..] -m [mode] -- [command]

Hyprshot is an utility to easily take screenshot in Hyprland using your mouse.

It allows taking screenshots of windows, regions and monitors which are saved to a folder of your choosing and copied to your clipboard.

Options:
  -h, --help            show help message
  -m, --mode            one of: output, window, region
  -o, --output-folder   directory in which to save screenshot
  -f, --filename        the file name of the resulting screenshot
  -d, --debug           print debug information
  -s, --silent          don't send notification when screenshot is saved
  -r, --raw             output raw image data to stdout
  -t, --notif-timeout   notification timeout in milliseconds (default 5000)
  -c, --current         select active window/output (not applicable to region)
  --clipboard-only      copy screenshot to clipboard and don't save image in disk
  -- [command]          open screenshot with a command of your choosing. e.g. hyprshot -m window -- mirage

Modes:
  output                take screenshot of an entire monitor
  window                take screenshot of an open window
  region                take screenshot of selected region
EOF
}

function Print() {
    if [ $DEBUG -eq 0 ]; then
        return 0
    fi
    
    1>&2 printf "$@" 
}

function send_notification() {
    if [ $SILENT -eq 1 ]; then
        return 0
    fi

    local message=$([ $CLIPBOARD -eq 1 ] && \
        echo "Image copied to the clipboard" || \
        echo "Image saved in <i>${1}</i> and copied to the clipboard.")
    notify-send "Screenshot saved" \
                "${message}" \
                -t "$NOTIF_TIMEOUT" -i "${1}" -a Hyprshot
}

function trim() {
    convert "${1}" -trim +repage "${1}"
}

function save_geometry() {
    Print "Geometry: %s\n" "${1}"
    local output=""

    if [ $RAW -eq 1 ]; then
        grim -g "${1}" - | trim -
        return 0
    fi

    if [ $CLIPBOARD -eq 0 ]; then
        mkdir -p "$SAVEDIR"
        grim -g "${1}" "$SAVE_FULLPATH"
        output="$SAVE_FULLPATH"
        # Trim transparent pixels, in case the window was floating and partially
        # outside the monitor
        trim "${output}"
        wl-copy < "$output"
        [ -z "$COMMAND" ] || {
            "$COMMAND" "$output"
        }
    else
        wl-copy < <(grim -g "${1}" - | trim -)
    fi

    send_notification $output
}

function begin_grab() {
    local option=$1
    case $option in
        output)
            if [ $CURRENT -eq 1 ]; then
                local geometry=`grab_active_output`
            else
                local geometry=`grab_output`
            fi
            ;;
        region)
            local geometry=`grab_region`
            ;;
        window)
            if [ $CURRENT -eq 1 ]; then
                local geometry=`grab_active_window`
            else
                local geometry=`grab_window`
            fi
            ;;
    esac
    save_geometry "${geometry}"
}

function grab_output() {
    slurp -or
}

function grab_active_output() {
    local active_workspace=`hyprctl -j activeworkspace`
    local monitors=`hyprctl -j monitors`
    Print "Monitors: %s\n" "$monitors"
    Print "Active workspace: %s\n" "$active_workspace"
    local current_monitor="$(echo $monitors | jq -r 'first(.[] | select(.activeWorkspace.id == '$(echo $active_workspace | jq -r '.id')'))')"
    Print "Current output: %s\n" "$current_monitor"
    echo $current_monitor | jq -r '"\(.x),\(.y) \(.width)x\(.height)"'
}

function grab_region() {
    slurp -d
}

function grab_window() {
    local monitors=`hyprctl -j monitors`
    local clients=`hyprctl -j clients | jq -r '[.[] | select(.workspace.id | contains('$(echo $monitors | jq -r 'map(.activeWorkspace.id) | join(",")')'))]'`
    Print "Monitors: %s\n" "$monitors"
    Print "Clients: %s\n" "$clients"
    # Generate boxes for each visible window and send that to slurp
    # through stdin
    local boxes="$(echo $clients | jq -r '.[] | "\(.at[0]),\(.at[1]) \(.size[0])x\(.size[1]) \(.title)"')"
    Print "Boxes:\n%s\n" "$boxes"
    slurp -r <<< "$boxes"
}

function grab_active_window() {
    local active_window=`hyprctl -j activewindow`
    local box=$(echo $active_window | jq -r '"\(.at[0]),\(.at[1]) \(.size[0])x\(.size[1])"')
    Print "Box:\n%s\n" "$box"
    echo "$box"
}

function args() {
    local options=$(getopt -o hf:o:m:dsrt:c --long help,filename:,output-folder:,mode:,clipboard-only,debug,silent,raw,notif-timeout:,current -- "$@")
    eval set -- "$options"

    while true; do
        case "$1" in
            -h | --help)
                Help
                exit
                ;;
            -o | --output-folder)
                shift;
                SAVEDIR=$1
                ;;
            -f | --filename)
                shift;
                FILENAME=$1
                ;;
            -m | --mode)
                shift;
                echo "${AVAILABLE_MODES[@]}" | grep -wq $1
                OPTION=$1;;
            --clipboard-only)
                CLIPBOARD=1
                ;;
            -d | --debug)
                DEBUG=1
                ;;
            -s | --silent)
                SILENT=1
                ;;
            -r | --raw)
                RAW=1
                ;;
            -t | --notif-timeout)
                shift;
                NOTIF_TIMEOUT=$1
                ;;
            -c | --current)
                CURRENT=1
                ;;
            --)
                shift # Skip -- argument
                COMMAND=${@:2}
                break;;
        esac
        shift
    done

    if [ -z $OPTION ]; then
        Print "A mode is required\n\nAvailable modes are:\n\toutput\n\tregion\n\twindow\n"
        exit 2
    fi
}

if [ -z $1 ]; then
    Help
    exit
fi

CLIPBOARD=0
DEBUG=0
SILENT=0
RAW=0
NOTIF_TIMEOUT=5000
CURRENT=0
FILENAME="$(date +'%Y-%m-%d-%H%M%S_hyprshot.png')"
[ -z "$HYPRSHOT_DIR" ] && SAVEDIR=${XDG_PICTURES_DIR:=~} || SAVEDIR=${HYPRSHOT_DIR}

args $0 "$@"

SAVE_FULLPATH="$SAVEDIR/$FILENAME"
[ $CLIPBOARD -eq 0 ] && Print "Saving in: %s\n" "$SAVE_FULLPATH"
begin_grab $OPTION
