# CLI Command Guide 💻

---

### General

#### Symlinks (Windows)

* **Soft File Link:** `mklink "<LinkPath>" "<OriginalFilePath>"`
* **Hard File Link:** `mklink /H "<LinkPath>" "<OriginalFilePath>"`
* **Soft Folder Link:** `mklink /D "<LinkPath>" "<RealFolderPath>"`
* **Hard Folder Link (Junction):** `mklink /J "<LinkPath>" "<RealFolderPath>"`
* **Explanation:**
  * **Soft vs. Hard Links:** A **soft link** is a shortcut to the original path. A **hard link** is a direct reference to the physical file data; the file is not removed until all hard links are deleted.
  * **Soft vs. Hard Folder Links:** For directories, there are no true hard links. A **directory junction** acts at a lower level than a soft link, making it appear more like a real folder to programs.
  * Deleting a soft link shortcut doesn't delete the original file. However, deleting a file from inside a linked folder (soft or hard) will delete the original file.

#### Symlinks (Linux)

* **Soft Link:** `ln -s "<OriginalFileOrFolderPath>" "<LinkPath>"`
* **Explanation:**
  * The `-s` flag indicates a soft link. When linking folders, you must use this flag as Linux folders do not have hard links. Omitting the flag when linking files creates a hard link.

---

### Archiving 📦

#### 7-Zip with Encryption

* **Archive:** `7z a -mx=0 -mhe=on -p"<MyPassword>" "<outputPath>" "<inputPath>"`
* **Extract:** `7z x <pathToArchive>`
* **Explanation:**
  * `-mhe=on` encrypts filenames.
  * `-p"<MyPassword>"` adds the password without prompting. Use `-p` alone for a secure password prompt.
  * `-mx=0` disables compression; `9` is max compression.
  * The `x` flag extracts the archive.

#### Tar Archiving

* **Archive:** `tar --create --gzip --file="<outputPath>.tar.gz" "<InputPath>"`
* **Extract:** `tar --extract --file="<pathToArchive>"`
* **Explanation:**
  * `-c[--create]` creates a new archive.
  * `-z[--gzip]` uses gzip compression.
  * `-f[--file]` specifies the archive file path.

#### Gzip Archiving

* **Archive:** `gzip <filename>`
* **Extract:** `gzip -d <filename>.gz`
* **Explanation:**
  * Gzip is for single files and does not support directories.
  * It replaces the original file with the compressed one and vice versa upon extraction.

---

### Searching 🔍

#### Search for a String in All Files

* **Command:** `grep --recursive --line-number --ignore-case --fixed-strings "<pattern>" "/"`
* **Explanation:**
  * `-R[--recursive]` traverses into directories.
  * `-n[--line-number]` prints the line number of matches.
  * `-i[--ignore-case]` ignores case in the pattern.
  * `-F[--fixed-strings]` treats the pattern as a literal string.
  * `-P[--perl-regexp]` uses regular expressions for the pattern.
  * `--include=<GLOBPattern>` searches only filenames matching the pattern.
  * `--exclude=<GLOBPattern>` excludes filenames matching the pattern.

#### Find File or Path by Name

* **Command:** `find / -name "filename*.jpg"`
* **Explanation:**
  * The first argument is the base search directory.
  * `-name` matches the basename (last part of the path). Use `-path` for full path matching. Supports wildcards `*`.
  * `-mindepth n` and `-maxdepth n` control search depth.
  * `-type <f|d|l>` filters by type: `f` (file), `d` (directory), `l` (symbolic link).
  * Use `!` to negate a flag (e.g., `! -type d`).

---

### Xargs

* **Example 1:** `find / -name "*.js" -print0 | xargs -I "{ARG}" --delimiter="\0" echo "found JS file: {ARG}"`
* **Example 2:** `printf "1\n2 3\n4 5" | xargs -I "{ARG}" --delimiter="\n" echo "current: {ARG}"`
* **Explanation:**
  * `xargs` runs commands based on piped input.
  * `-I` replaces the default `{}` placeholder.
  * `--delimiter="<character>"` specifies the character to split the input on.

---

### Downloading

* **Command:** `wget -c --tries=0 --retry-connrefused --timeout=10 --wait=3 <URL>`
* **Explanation:**
  * `-c` continues a paused download.
  * `--tries=0` retries indefinitely.
  * `--retry-connrefused` retries if the connection is refused.
  * `--timeout=10` reconnects if no data is received for 10 seconds.
  * `--wait=3` waits 3 seconds before reconnecting.

---

### Metadata

* **Command:** `exiftool -api LargeFileSupport=1 -extractEmbedded -G "<FileOrFolderPath>"`
* **Explanation:**
  * Lists all file metadata.
  * `-api LargeFileSupport=1` enables support for large files.
  * `-extractEmbedded` extracts tags from embedded files.
  * `-G` adds a group name to each tag for clarity.
  * `-TagNameHere` retrieves a specific tag.
  * `-TagNameHere=value` writes a new value.
  * `-TagNameHere=` deletes a tag.

---

### GIT

* **Commit Changes:** `git commit -m "<message>"`
* **Create Local Branch:** `git checkout -b <branchName>`
* **Push Local Branch to Remote:** `git push -u[--set-upstream] origin <branchName>`
* **Switch to Branch:** `git checkout <branchName>`

---

### Media 🎬

#### Trim Video without Re-encoding

* **Command:** `ffmpeg -ss <StartTimeStamp> -to <EndTimeStamp> -i "<inputVideoPath>" -c copy "<OutputVideoPath>"`
* **Explanation:**
  * Timestamps are in the format "00:00:00.000".
  * `-c copy` copies the video and audio codecs directly, avoiding re-encoding.

#### Get Frame at Timestamp

* **Command:** `ffmpeg -ss <TimeStamp> -i <inputVideoPath> -vframes 1 output_frame_%03d.jpg`
* **Explanation:**
  * `-ss` specifies the start timestamp.
  * `-vframes 1` captures one frame.
  * `-s <Width>x<Height>` sets the frame dimensions.

#### Change Video Container without Re-encoding

* **Command:** `ffmpeg -i "<InputVideoPath>" -c copy "<OutputVideoPath>"`
* **Explanation:**
  * Copies both video and audio streams to a new container.

#### Copy Video without Metadata

* **Command:** `ffmpeg -i "<InputVideoPath>" -map_metadata -1 -c copy "<OutputVideoPath>"`
* **Explanation:**
  * `-map_metadata -1` discards all metadata.

#### Download M3U8 Stream

* **FFmpeg:** `ffmpeg -i "<url.m3u8>" -c copy "<OutputVideoPath>"`
* **yt-dlp:** `yt-dlp "<url.m3u8>" --output "<OutputVideoPath>"`
* **Explanation:**
  * Both commands download and combine a stream without re-encoding.
  * **FFmpeg** auto-selects quality. **yt-dlp** selects the highest quality by default.
  * `yt-dlp`'s `--no-mtime` sets the file's modified timestamp to the current time.

#### Video Transformations (Scale, Flip)

* **GPU:** `ffmpeg -hwaccel cuda -i "<inputVideoPath>" -vf "scale=1920:1080,hflip" -c:v h264_nvenc -rc:v vbr -qmin:v 19 -qmax:v 25 -c:a copy "<outputVideoPath>"`
* **CPU:** `ffmpeg -i "<inputVideoPath>" -vf "scale=1920:1080,hflip" -c:v libx264 -crf 23 -c:a copy "<outputVideoPath>"`
* **Explanation:**
  * `-vf` applies a chain of video filters (e.g., `scale=X:Y` and `hflip`).
  * `-c:v` selects the video encoder. **GPU encoders** are fast but may have format limitations; **CPU encoders** are slower but highly compatible.
  * `-vsync vfr` is used for videos with variable framerate.
  * **CPU Options:** `-crf` (Constant Rate Factor) sets quality from 0 (best) to 51 (worst).
  * **GPU Options:** `-hwaccel cuda` enables GPU acceleration. `-rc:v vbr` and `-qmin:v / -qmax:v` control quality and bitrate for the GPU encoder. A quality level of 19 is visually lossless.

---

### Linux System 🐧

#### Group Management

* **Add Group:** `groupadd <GroupName>`
* **Delete Group:** `groupdel <GroupName>`
* **List Members:** `members <groupname>`

#### User Management

* **Add User:** `useradd <username>`
* **Explanation:**
  * `--no-create-home[-M]`: Don't create a home directory.
  * `--create-home[-m]`: Create a home directory.
  * `--no-user-group[-N]`: Don't create a group with the same name.
  * `--user-group[-U]`: Create a group with the same name.
  * `--base-dir[-b]`: Specify a different base directory for home folders.
  * `--groups[-G] <GroupName>`: Add the user to specified groups.

* **Add User to Group:** `usermod --append --groups <groupname1> <username>`
* **Explanation:**
  * `--append[-a]` is crucial to avoid removing the user from other groups.

* **Remove User from Group:** `deluser <user> <group>`

---

### Bandwidth Limiting 🐌

#### Limit for Command/Application

* **Command:** `trickle -u 750 -d 100000 google-chrome`
* **Explanation:**
  * `-u <KB/S>` sets upload speed in Kilobytes/second.
  * `-d <KB/S>` sets download speed in Kilobytes/second.

#### Limit System-wide

* **Command:** `wondershaper -a <adapter> -d 100000 -u 9000`
* **Explanation:**
  * `-a <adapter>` specifies the network adapter (use `ifconfig`).
  * `-d` sets download speed in Kilobits/second.
  * `-u` sets upload speed in Kilobits/second.
  * To clear throttling, use `wondershaper -c -a <adapter>`.

---

### Execution

* **Run as Specific User:** `runuser -u www-data -- echo hello world`
* **Explanation:**
  * `-u <UserName>` runs the command as the specified user. Everything after `--` is the command.

* **Schedule Command with `at`:**
  * `echo "<MyCommand>" | at now`
  * `echo "<MyCommand>" | at 09:00`
  * `echo "<MyCommand>" | at now +<Count> <TimeUnit>`
* **Explanation:**
  * `at` schedules a one-time job. Time units can be `minutes`, `hours`, `days`, `weeks`.

---

### Usage Guide 📘

* **`<argument>`:** A placeholder for user input.
* **`<option1|option2>`:** A choice between predefined options.
* **`-s[--save]`:** A flag with an optional long-form alias in square brackets.
