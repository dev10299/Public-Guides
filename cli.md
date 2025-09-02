### General ###

Symlinks (Windows):
    * Command(soft filelink)  : mklink "<LinkPath>" "<OriginalFilePath>"
    * Command(hard filelink)  : mklink /H "<LinkPath>" "<OriginalFilePath>"
    * Command(soft folderLink): mklink /D "<LinkPath>" "<RealFolderPath>"
    * Command(hard folderLink): mklink /J "<LinkPath>" "<RealFolderPath>"
    * Explanation:
        * soft vs hard file links: A soft link creates a shortcut to the original path mimicking being in another location/path. 
          A hard link is an actual reference to the physical disc location of that file. The file is not removed until all hardlinks are deleted.
        * soft vs hard folder link: For directories there are no actual "hard" links, it's called a directory junction. Both are similar with only
          difference being that soft folderlinks are very high level shortcuts that are transparant about being a symlink, vs a directory junction 
          acts at a much lower level therefore looking more like a real folder and can potentially trick programs into believing its a real folder
        * deleting a soft link shortcut never deletes the original file, beware though, traversing into a soft/hard link folder and deleting files 
          inside of it will obviously delete original files.

Symlinks (Linux):
    * Command(softlink): ln -s "<OriginalFileOrFolderPath>" "<LinkPath>"
    * Explanation:
        * -s indicates soft link, creates a shortcut to original file or folder.
          When linking folders, you must use the soft flag, since folders in linux dont have hard links.
          When linking files you can exclude this flag and therefore making it a hard link(same concept as windows).

7zip Archiving with encryption:
    * Command(Archive): 7z a -mx=0 -mhe=on -p"<MyPassword>" "<outputPath>" "<inputPath>"
    * Command(Extract): 7z x <pathToArchive>
    * Explanation: 
        * -mhe flag encrypts filenames
        * -p flag with password suffix ads password without prompting, if you want password prompt, then only specify -p (safer)
        * -mx=0 indicates no compression, where 9 uses max compression
        * x flag extracts archive

Tar Archiving:
    * Command(Archive): Tar --create --gzip --file="<outputPath>.tar.gz" "<InputPath>"
    * Command(Extract): tar --extract --file="<pathToArchive>"
    * Explanation:
        * input path, where to gather files from
        * -c[--create] creates a new archive
        * -z[--gzip] uses gzip
        * -f[--file] specify archive filepath

Gzip Archiving:
   * Command(Archive): gzip <filename1>
   * Command(Extract): gzip -d <filename>.gz
   * Explanation:
      * Gzip for simple and quick tasks of individual files,
        directories not supported, use instead 7z or tar.gz etc...).
      * Beware that it will replace original file with archived one,
        same goes for extracting where archive will be replaced by extracted file.

Search for a string in all files:
    * Command: grep --recursive --line-number --ignore-case --fixed-strings "<pattern>" "/"
    * Explanation:
        * last arg is directory to start scan from
        * -R[--recursive], traverses all the way into directories
        * -n[--line-number], prints line number with matched patterns in file
        * -i[--ignore-case], ignore case in patterns
        * -F[--fixed-strings], use regular string comparison for the pattern
        * -P[--perl-regexp], if you wish to match with regexp, use this flag instead
        * --include=<GLOBPattern>, Search only for filenames that matches this pattern
        * --exclude=<GLOBPattern>, Do not include filenames that matches this pattern
        
        
Finding any file or path by name:
    * Command: find / -name "filename*.jpg"
    * Explanation:
        * first arg specifies base search dir
        * -name: matches the basename of files/folders eg the last part of path, use -path flag instead for full path matching, supports wildcards with * asterisk
        * -mindepth n: which level in tree and below to search for, (level 0 includes the path itself that we started our search from)
        * -maxdepth n: How deep in the folder tree it should search for
        * -type <f|d|l>, specify one of the types to filter items on, f=regular files, d=directories, l=symbolic links 
          flags such as -type, -path, -name etc can be negated or be used for reverse effect with NOT operator(!), 
          such as "! -type l ! -type d ! -path "*/cache/*" would mean, everything except symbolic links and directories and exclude "*/cache/*" path

Running commands based of output from another with XArgs:
    * Command(example1): find / -name "*.js" -print0 | xargs -I "{ARG}" --delimiter="\0" echo "found JS file: {ARG}" 
    * Command(example2): printf "1\n2 3\n4 5" | xargs -I "{ARG}" --delimiter="\n" echo "current: {ARG}"
    * Explanation:
        * Runs commands based of a delimiter of the piped input
        * example2 would have run commands["1", "2 3", "4 5"] 
        * -I replaces the default {} placeholder
        * --delimiter="<character>", specifies what character to split the piped input on everything after the flags are a freely typed command

Downloading file from url with retry mechanism:
    * Command: wget -c --tries=0 --retry-connrefused --timeout=10 --wait=3 <URL>
    * Explanation:
        * -c tells wget to continue a download, so you can stop wget and restart it later without downloading the whole file again.
        * –tries=0 means that the download will be tried indefinitely if the connection breaks.
        * –retry-connrefused forces wget to retry even if the server is currently not listening.
        * –timeout=10 tells wget to reconnect if no data is received for more than 10 seconds.
        * –wait=3 means wget will sleep for 3 seconds before it reconnects.

Alter or retrieve file/media metadata:
    * command: exiftool -api LargeFileSupport=1 -extractEmbedded -G "<FileOrFolderPath>"
    * Explanation:
        * Lists all of the metadata belonging to the files
        * -api LargeFileSupport=1, supports larger files in size, no reason to not use it
        * -extractEmbedded extract tags form embedded files aswell 
        * -G add group name of tag in each line, makes it easier to distinguish between the tags
        * -TagNameHere to retrieve a tag with specified name
        * -TagNameHere=value to write a new value to tag
        * -TagNameHere= to delete a tag


### GIT ###
Commit Changes:
    * Command: git commit -m "<message>"
Create local branch: 
    * Command: git checkout -b <branchName>
Push local branch to remote
    * Command: git push -u[--set-upstream] origin <branchName>
Switch to branch:
    * Command: git checkout <branchName>


### Media ###
Trim Video from start to finish mark without re-encoding
    * Command: ffmpeg -ss <StartTimeStamp> -to <EndTimeStamp> -i "<inputVideoPath>" -c copy "<OutputVideoPath>"
    * Explanation:
        * timestamps for start and end needs to be in format "00:00:00.000"
        * -c copy, is shorthand for "-vcodec copy -acodec copy" which implies to copy video and audio codec

Get frame at timestamp
   * Command: ffmpeg -ss <TimeStamp> -i <inputVideoPath> -vframes 1 output_frame_%03d.jpg
   * Explanation:
      * timestamps specified in format "00:00:00.000", is the start point from where frames are captured
      * -vframes <FrameCount>, How many frames to capture from start timestamp
      * -s <Width>x<Height>, specify pixel size of each frame

Change Video Container without reencoding, eg mkv to mp4 etc…
    * Command: ffmpeg -i "<InputVideoPath>" -c copy "<OutputVideoPath>"
    * Explanation:
        * -c copies both video and audio encodings over to the converted video

Copy Video without metadata
    * Command: ffmpeg -i "<InputVideoPath>" -map_metadata -1 -c copy "<OutputVideoPath>"

Download M3U8 Stream [ffmpeg]:
   * Command: ffmpeg -i "<url.m3u8>" -c copy "<OutputVideoPath>"
   * Explanation:
      * Downloads and combines a stream without reencoding
      * Either a direct stream url or a playlist url can be provided, keep in mind that when playlist is provided the output quality auto selects quality and may give poor viewing experience 

Download M3U8 Stream [yt-dlp]:
   * Command: yt-dlp "<url.m3u8>" --output "<OutputVideoPath>"
   * Explanation:
      * Downloads and combines a stream without reencoding
      * Selects highest quality stream by default
      * --no-mtime sets modified timestamp to current datetime instead of what server provides


Video Transformations (scale, mirror horizontal flip):
    * Command (GPU Version): ffmpeg -hwaccel cuda -i "<inputVideoPath>" -vf "scale=1920:1080,hflip" -c:v h264_nvenc -rc:v vbr -qmin:v 19 -qmax:v 25 -c:a copy "<outputVideoPath>"
    * Command (CPU Version): ffmpeg -i "<inputVideoPath>" -vf "scale=1920:1080,hflip" -c:v libx264 -crf 23 -c:a copy "<outputVideoPath>"
    * Explanation:
        * -c:a copy, simply copies over the audio stream as it is
        * -vf "<filter1>,<filter2>...", freely choose filter to apply to video, one or more filters can be set
            * scale=X:Y, resizes video to x & y size
            * hflip, flips the video horizontally
        * -r <fps>, convert video to desired fps
        * -c:v <encoder>, chooses video encoder. GPU encoders are fast but are not compatible with as many video formats.
          if a video is not supported you can use libx264 encoder instead which uses cpu(much slower) and is compatible with pretty much any video
        * -vsync vfr, use this flag after input source when encountering videos with variable framerate (seems to require cpu encoder)
        * CPU encoder specific options:
           * -crf <best 0 to worst 51>, a quality setting to determine to overall quality of the encoded video, 20 is a good balance for quality and size
        * GPU encoder specific options: 
           * -hwaccel cuda, decodes video frames with GPU support if available in system
           * -rc:v <RateControlMode>, vbr in our case is recommended since it varies bitrate when its needed to achieve a desired quality level.
             when using encoder h264_nvenc we MUST specify "-rc:v vbr" to be able to use -qmin and -qmax options. 
             If we would for example use libx264(cpu encoder) we would not need to even specify this setting since it doesn't use those options anyway.
           * -qmin:v / -qmax:v <best 0 to worst 51>, dictates the best to worst quality allowed to fluctate between depending on complexity of scene.
             For a simple reference, the quality level 19 is visually lossless.
             The example options of -qmin:v 19 -qmax:v 25 is a good balance for quality and size.



### Linux System ###
Add a new group
   * Command: groupadd <GroupName>
Delete group:
   * Command: groupdel <GroupName>
List group members:
   * Command: members <groupname>

Add a new user:
   * Command: useradd <username>
   * Explanation:
      * --no-create-home[-M]: dont create a home directory, 
      * --create-home[-m]: create user a home directory
      * --no-user-group[-N]: do not create a group with the same name as the user
      * --user-group[-U]: Creates a group with the same name as the user
      * --base-dir[-b]:  if you don't want the systems default homedir path you can specify another directory to store users in,
        example: --base-dir "/testUsers", which would then result in users home being created under that folder eg. "/testUsers/user1".
        if you want even more control you can use the --home-dir[-d] option to set the exact path of the homedir, example: --home-dir "/testUsers/user1"
      * --groups[-G] <GroupName>: makes the new user part of specified groups

Add existing user to a group
   * Command: usermod --append --groups <groupname1> <username>
   * Explanation:
      * --append[-a] Always use the --append flag when adding a user to a new group. If you omit the --append flag, the user will be
        removed from any groups not listed after the --groups option.
      * --groups[-G], Groupnames to add, also accepts csv values to add user to multiple groups at once, example: <group1,group2>
Remove user from group:
   * Command: deluser <user> <group>

Limit bandwidth speed for command or application:
   * Command: trickle -u 750 -d 100000 google-chrome
   * Explanation:
      * in this example downloads in 100 MB/S and uploads in 750KB/S. Not to confuse with megabits, these are in megabytes aka 8x larger, 
      * both upload and download parameter must be set, if you want unlimited speed one way, simple set a huge number
      * -u <KB/S>: sets the upload speed limit, specified in Kilobytes per second.
      * -d <KB/S>: sets the download speed limit, specified in Kilobytes per second.
 
Limit systemwide bandwith:
   * Command: wondershaper -a enx00e04cbc3a87 -d 100000 -u 9000
   * Explanation:
      * -a <adapter>: specify network adapter to place limit on, use command ifconfig to get name of adapter
      * -d <KilobitsPerSeconds>: specifies download speed in kiloBits(8x smaller than kiloByte)
      * -u <KilobitsPerSeconds>: specfies upload speed in kiloBits(8x smaller than kiloByte)
      * To then clear the adapter throttling: wondershaper -c -a <adapter>

Run command as specific user:
   * Command: runuser -u www-data -- echo hello world
   * Explanation:
      * <UserName>: run the following command as this user
      * everything after -- is the command to be executed by the specified user. 

Schedule command run execution:
   * Command1: echo "<MyCommand>" | at now
   * Command2: echo "<MyCommand>" | at 09:00
   * command3: echo "<MyCommand>" | at now +<Count> <TimeUnit>
   * Explanation:
      * +<Count> <TimeUnit>: adds time starting from now, eg "at now +5 minutes", runs the job after 5 minutes, TimeUnit be one of following [minutes, hours, days, weeks]
      * The processing running the command is different from interactive shell and therefore does not block the shell

### Usage Guide ###
The command field has the following format:
    * inbetween angle brackets "<>" is the placeholder for the user to set anything to fit their needs, eg: Command <yourArgumentHere>
    * When choosing between predefined constants the OR symbol is used in angle brackets such as: UserType <admin|normal>,
      which means that UserType command should be followed by either admin or normal
    * if there is an alias flag, it will be directly following the other flag with square brackets "[]", eg: Command -s[--save], where -s is same as --save