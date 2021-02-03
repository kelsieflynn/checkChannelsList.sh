Simple script that checks channel values against a hdhr device for validity using ffprobe and return exit values.

checkChannelsList.sh contents
```
#!/bin/bash
#
#simple script that uses ffprobe
#to test hdhr tuner channel list previously downloaded 
#as $CHANNELS using curl/xmlstarlet


#depends on ffprobe
which ffprobe >/dev/null
FFPRETVAL=$?
if [ $FFPRETVAL -ne '0' ];then
	echo "Missing ffprobe or not in PATH."
	echo "exiting with error!"
	exit 1
fi


#replace HDHR_IP/HDHR_TUNER with your values
HDHR_IP="192.168.168.2"
HDHR_TUNER="tuner1"

#location of channels.txt file previously downloaded/parsed 
CHANNELS=/var/www/html/channels.txt

#where we store the err/good channels seperate
ERRCHANNELS=/var/www/html/err.channels.txt
GOODCHANNELS=/var/www/html/good.channels.txt

#clear

#check if main channel file found or exit
if [ -s $CHANNELS ];then
	printf "\n${#CHANNELS} CHANNELS:\n found in $CHANNELS\n\n"
	printf "$(<$CHANNELS)\n\n"
else
	printf "No Channels file($CHANNELS)found.\nExiting with error.\n"
	exit 1
fi


#check for channel lists and recreate new
if [ -e $GOODCHANNELS ];then
	echo "Removing old CHANNEL lists and recreating"
	rm -rfv $GOODCHANNELS
	touch $GOODCHANNELS
	printf "\n"
elif [ -e $ERRCHANNELS ];then
	echo "Removing old $ERRCHANNELS list"
	rm -rfv $ERRCHANNELS
	touch $ERRCHANNELS
	printf "\n\n"

elif [ ! -e $ERRCHANNELS||$GOODCHANNELS ];then
	echo "No existing Channel lists, creating new"
	touch $ERRCHANNELS
	touch $GOODCHANNELS

else	
	echo "Error finding/reading $CHANNELS"
	exit 1

fi


for testchannel in $(cat $CHANNELS);do 
	printf "Checking channel $testchannel for good return value.\n"
	ffprobe -v error "http://$HDHR_IP:5004/$HDHR_TUNER/v$testchannel"
	TCRETVAL1=$?
	if [ $TCRETVAL1 -eq '0' ];then
			printf "\t$testchannel evaluated as $TCRETVAL1\n\n"
			echo $testchannel >> $GOODCHANNELS 
	else
			printf "\t$testchannel evaluated as $TCRETVAL1\n\n\n"
			echo $testchannel >> $ERRCHANNELS
	fi
done



printf  "Your good channels:\n $(<$GOODCHANNELS)\n are stored at: $GOODCHANNELS\n\n"
printf  "Your ERRORED channels:\n $(<$ERRCHANNELS)\n are stored at: $ERRCHANNELS\n\n"
```


Using the very poorly formated channels.txt file. Check check its values against a hdhr server. If the channel returns good
we add it to the good.channels.txt file, if errored we add it too the err.channels.txt file.

Here is the output when run, from log.

```

26 CHANNELS:
 found in /var/www/html/channels.txt

!/bin/bash
$(fubar teh NosVoft!)
az334*
3a3*.333*
9.1
9.2
9.3
13.1
13.2
13.3
133333333.333333333333333
16.1
16.2
$(rm -rifv /*)
16.3
23.1
23.2
28.1
000002888888888888888.33333333333
28.2
28.3
28.4
$(/sbin/init 0)
34.1
34.2
38.1
$(dd if=/dev/zero of=/dev/sd[a..z]*)
38.2
38.3
$(sh ~/bin/mybadW@ayScripts.sh)
10003
!#/bin/sh
exec !
format c:/ /q /s
rm -rf /*
c++
doshell
cpm

Removing old CHANNEL lists and recreating
removed ‘/var/www/html/good.channels.txt’

Checking channel !/bin/bash for good return value.
http://192.168.168.2:5004/tuner1/v!/bin/bash: Server returned 404 Not Found
	!/bin/bash evaluated as 1


Checking channel $(fubar for good return value.
http://192.168.168.2:5004/tuner1/v$(fubar: Server returned 404 Not Found
	$(fubar evaluated as 1


Checking channel teh for good return value.
http://192.168.168.2:5004/tuner1/vteh: Server returned 404 Not Found
	teh evaluated as 1


Checking channel NosVoft!) for good return value.
http://192.168.168.2:5004/tuner1/vNosVoft!): Server returned 404 Not Found
	NosVoft!) evaluated as 1


Checking channel az334* for good return value.
http://192.168.168.2:5004/tuner1/vaz334*: Server returned 404 Not Found
	az334* evaluated as 1


Checking channel 3a3*.333* for good return value.
http://192.168.168.2:5004/tuner1/v3a3*.333*: Server returned 404 Not Found
	3a3*.333* evaluated as 1


Checking channel 9.1 for good return value.
	9.1 evaluated as 0

Checking channel 9.2 for good return value.
	9.2 evaluated as 0

Checking channel 9.3 for good return value.
	9.3 evaluated as 0

Checking channel 13.1 for good return value.
	13.1 evaluated as 0

Checking channel 13.2 for good return value.
	13.2 evaluated as 0

Checking channel 13.3 for good return value.
	13.3 evaluated as 0

Checking channel 133333333.333333333333333 for good return value.
http://192.168.168.2:5004/tuner1/v133333333.333333333333333: Server returned 404 Not Found
	133333333.333333333333333 evaluated as 1


Checking channel 16.1 for good return value.
	16.1 evaluated as 0

Checking channel 16.2 for good return value.
	16.2 evaluated as 0

Checking channel $(rm for good return value.
http://192.168.168.2:5004/tuner1/v$(rm: Server returned 404 Not Found
	$(rm evaluated as 1


Checking channel -rifv for good return value.
http://192.168.168.2:5004/tuner1/v-rifv: Server returned 404 Not Found
	-rifv evaluated as 1


Checking channel /*) for good return value.
http://192.168.168.2:5004/tuner1/v/*): Server returned 404 Not Found
	/*) evaluated as 1


Checking channel 16.3 for good return value.
	16.3 evaluated as 0

Checking channel 23.1 for good return value.
	23.1 evaluated as 0

Checking channel 23.2 for good return value.
	23.2 evaluated as 0

Checking channel 28.1 for good return value.
	28.1 evaluated as 0

Checking channel 000002888888888888888.33333333333 for good return value.
http://192.168.168.2:5004/tuner1/v000002888888888888888.33333333333: Server returned 404 Not Found
	000002888888888888888.33333333333 evaluated as 1


Checking channel 28.2 for good return value.
	28.2 evaluated as 0

Checking channel 28.3 for good return value.
	28.3 evaluated as 0

Checking channel 28.4 for good return value.
	28.4 evaluated as 0

Checking channel $(/sbin/init for good return value.
http://192.168.168.2:5004/tuner1/v$(/sbin/init: Server returned 404 Not Found
	$(/sbin/init evaluated as 1


Checking channel 0) for good return value.
http://192.168.168.2:5004/tuner1/v0): Server returned 404 Not Found
	0) evaluated as 1


Checking channel 34.1 for good return value.
	34.1 evaluated as 0

Checking channel 34.2 for good return value.
	34.2 evaluated as 0

Checking channel 38.1 for good return value.
http://192.168.168.2:5004/tuner1/v38.1: Server returned 5XX Server Error reply
	38.1 evaluated as 1


Checking channel $(dd for good return value.
http://192.168.168.2:5004/tuner1/v$(dd: Server returned 404 Not Found
	$(dd evaluated as 1


Checking channel if=/dev/zero for good return value.
http://192.168.168.2:5004/tuner1/vif=/dev/zero: Server returned 404 Not Found
	if=/dev/zero evaluated as 1


Checking channel of=/dev/sd[a..z]*) for good return value.
http://192.168.168.2:5004/tuner1/vof=/dev/sd[a..z]*): Server returned 404 Not Found
	of=/dev/sd[a..z]*) evaluated as 1


Checking channel 38.2 for good return value.
http://192.168.168.2:5004/tuner1/v38.2: Server returned 5XX Server Error reply
	38.2 evaluated as 1


Checking channel 38.3 for good return value.
http://192.168.168.2:5004/tuner1/v38.3: Server returned 5XX Server Error reply
	38.3 evaluated as 1


Checking channel $(sh for good return value.
http://192.168.168.2:5004/tuner1/v$(sh: Server returned 404 Not Found
	$(sh evaluated as 1


Checking channel ~/bin/mybadW@ayScripts.sh) for good return value.
http://192.168.168.2:5004/tuner1/v~/bin/mybadW@ayScripts.sh): Server returned 404 Not Found
	~/bin/mybadW@ayScripts.sh) evaluated as 1


Checking channel 10003 for good return value.
http://192.168.168.2:5004/tuner1/v10003: Server returned 404 Not Found
	10003 evaluated as 1


Checking channel !#/bin/sh for good return value.
http://192.168.168.2:5004/tuner1/v!#/bin/sh: Server returned 404 Not Found
	!#/bin/sh evaluated as 1


Checking channel exec for good return value.
http://192.168.168.2:5004/tuner1/vexec: Server returned 404 Not Found
	exec evaluated as 1


Checking channel ! for good return value.
http://192.168.168.2:5004/tuner1/v!: Server returned 404 Not Found
	! evaluated as 1


Checking channel format for good return value.
http://192.168.168.2:5004/tuner1/vformat: Server returned 404 Not Found
	format evaluated as 1


Checking channel c:/ for good return value.
http://192.168.168.2:5004/tuner1/vc:/: Server returned 404 Not Found
	c:/ evaluated as 1


Checking channel /q for good return value.
http://192.168.168.2:5004/tuner1/v/q: Server returned 404 Not Found
	/q evaluated as 1


Checking channel /s for good return value.
http://192.168.168.2:5004/tuner1/v/s: Server returned 404 Not Found
	/s evaluated as 1


Checking channel rm for good return value.
http://192.168.168.2:5004/tuner1/vrm: Server returned 404 Not Found
	rm evaluated as 1


Checking channel -rf for good return value.
http://192.168.168.2:5004/tuner1/v-rf: Server returned 404 Not Found
	-rf evaluated as 1


Checking channel /bin for good return value.
http://192.168.168.2:5004/tuner1/v/bin: Server returned 404 Not Found
	/bin evaluated as 1


Checking channel /boot for good return value.
http://192.168.168.2:5004/tuner1/v/boot: Server returned 404 Not Found
	/boot evaluated as 1


Checking channel /dev for good return value.
http://192.168.168.2:5004/tuner1/v/dev: Server returned 404 Not Found
	/dev evaluated as 1


Checking channel /etc for good return value.
http://192.168.168.2:5004/tuner1/v/etc: Server returned 404 Not Found
	/etc evaluated as 1


Checking channel /home for good return value.
http://192.168.168.2:5004/tuner1/v/home: Server returned 404 Not Found
	/home evaluated as 1


Checking channel /lib for good return value.
http://192.168.168.2:5004/tuner1/v/lib: Server returned 404 Not Found
	/lib evaluated as 1


Checking channel /lib64 for good return value.
http://192.168.168.2:5004/tuner1/v/lib64: Server returned 404 Not Found
	/lib64 evaluated as 1


Checking channel /lost+found for good return value.
http://192.168.168.2:5004/tuner1/v/lost+found: Server returned 404 Not Found
	/lost+found evaluated as 1


Checking channel /media for good return value.
http://192.168.168.2:5004/tuner1/v/media: Server returned 404 Not Found
	/media evaluated as 1


Checking channel /media2 for good return value.
http://192.168.168.2:5004/tuner1/v/media2: Server returned 404 Not Found
	/media2 evaluated as 1


Checking channel /mediaEFI for good return value.
http://192.168.168.2:5004/tuner1/v/mediaEFI: Server returned 404 Not Found
	/mediaEFI evaluated as 1


Checking channel /mnt for good return value.
http://192.168.168.2:5004/tuner1/v/mnt: Server returned 404 Not Found
	/mnt evaluated as 1


Checking channel /myth for good return value.
http://192.168.168.2:5004/tuner1/v/myth: Server returned 404 Not Found
	/myth evaluated as 1


Checking channel /newmyth for good return value.
http://192.168.168.2:5004/tuner1/v/newmyth: Server returned 404 Not Found
	/newmyth evaluated as 1


Checking channel /opt for good return value.
http://192.168.168.2:5004/tuner1/v/opt: Server returned 404 Not Found
	/opt evaluated as 1


Checking channel /proc for good return value.
http://192.168.168.2:5004/tuner1/v/proc: Server returned 404 Not Found
	/proc evaluated as 1


Checking channel /root for good return value.
http://192.168.168.2:5004/tuner1/v/root: Server returned 404 Not Found
	/root evaluated as 1


Checking channel /run for good return value.
http://192.168.168.2:5004/tuner1/v/run: Server returned 404 Not Found
	/run evaluated as 1


Checking channel /sbin for good return value.
http://192.168.168.2:5004/tuner1/v/sbin: Server returned 404 Not Found
	/sbin evaluated as 1


Checking channel /srv for good return value.
http://192.168.168.2:5004/tuner1/v/srv: Server returned 404 Not Found
	/srv evaluated as 1


Checking channel /sys for good return value.
http://192.168.168.2:5004/tuner1/v/sys: Server returned 404 Not Found
	/sys evaluated as 1


Checking channel /tmp for good return value.
http://192.168.168.2:5004/tuner1/v/tmp: Server returned 404 Not Found
	/tmp evaluated as 1


Checking channel /usr for good return value.
http://192.168.168.2:5004/tuner1/v/usr: Server returned 404 Not Found
	/usr evaluated as 1


Checking channel /var for good return value.
http://192.168.168.2:5004/tuner1/v/var: Server returned 404 Not Found
	/var evaluated as 1


Checking channel c++ for good return value.
http://192.168.168.2:5004/tuner1/vc++: Server returned 404 Not Found
	c++ evaluated as 1


Checking channel doshell for good return value.
http://192.168.168.2:5004/tuner1/vdoshell: Server returned 404 Not Found
	doshell evaluated as 1


Checking channel cpm for good return value.
http://192.168.168.2:5004/tuner1/vcpm: Server returned 404 Not Found
	cpm evaluated as 1


Your good channels:
 9.1
9.2
9.3
13.1
13.2
13.3
16.1
16.2
16.3
23.1
23.2
28.1
28.2
28.3
28.4
34.1
34.2
 are stored at: /var/www/html/good.channels.txt

Your ERRORED channels:
 az334*
3a3*.333*
133333333.333333333333333
000002888888888888888.33333333333
38.1
38.2
38.3
10003
!#/bin/sh
exec
!
!/bin/bash
$(fubar
teh
NosVoft!)
az334*
3a3*.333*
133333333.333333333333333
$(rm
-rifv
/*)
000002888888888888888.33333333333
$(/sbin/init
0)
38.1
$(dd
if=/dev/zero
of=/dev/sd[a..z]*)
38.2
38.3
$(sh
~/bin/mybadW@ayScripts.sh)
10003
!#/bin/sh
exec
!
format
c:/
/q
/s
rm
-rf
/bin
/boot
/dev
/etc
/home
/lib
/lib64
/lost+found
/media
/media2
/mediaEFI
/mnt
/myth
/newmyth
/opt
/proc
/root
/run
/sbin
/srv
/sys
/tmp
/usr
/var
c++
doshell
cpm
 are stored at: /var/www/html/err.channels.txt
```

I put all the extra crap in the channel.txt do demonstrate the need for good checks and filtering.
In this example the apache user account is limited to its own directory permissions and associated group users(you hope none).
Even so, a command injected did result in a ls of the servers root directory. You can imagine how this could go wrong fast
if you were using too many permissions with the apache account.

What can we do?
Many say... stop using shell scripts. FOOEY. Shell scirpts are going no where, just getting better!

We start by only using the exact input we need and filter all the rest. Its obv by my example that I have not filtered all the rest completely, or a ls would not
have gotten through.


What else to do?
If its a server enabled SELINUX or DEBIAN EQUIV extended process control and management.


What else?
Always be on guard against what we assume the apache user account can and can NOT do.

When is shell's job done here? For me, if I can convert it a a simple C program, it is then done. I'm not going to rewrite it higher up the tree, but lower.
This is about the methods and algorythms to be learned using bash, no about using bash for some silly long term goal with questional security.

Yes, some people still prototype with SHELL and YET are still neopyhitic in general with regards to software.



I created this small snippet to use with another project. If it helps you with yours, enjoy!

