ffmpeg -display_rotation -90 -i INPUT -c copy OUTPUT
ffmpeg -i INPUT -vf scale=1440:-1 OUTPUT
ffmpeg -i INPUT -ss 00:05:10 -to 00:15:30 -c copy OUPUT

$ cat mylist.txt
file '/path/to/file1'
file '/path/to/file2'
file '/path/to/file3'

ffmpeg -f concat -i pink.txt -c copy pink.out.webm