# Console Server

This is a replacement for the very old "cu" program (original part of
UUCP!).

Use this to turn a Linux box into a console server, so that you can "telnet
linux-box 2002" to connect to ttyS2.  Also, it can be used to connect to the
serial port directly from the shell.

There is on-line help.  You will see when you run the program.

## For console server:

Put this into /etc/xinetd.d/direct_ttyS2

	# default: on
	# description: The telnet server serves telnet sessions; it uses \
	#       unencrypted username/password pairs for authentication.
	service direct_ttyS2
	{
	        flags           = REUSE
	        type            = UNLISTED
	        socket_type     = stream        
	        wait            = no
	        user            = root
	        port            = 2002
	        protocol        = tcp
	        server          = /usr/sbin/in.telnetd
	        server_args     = -L /root/c2
	        log_on_failure  += USERID
	        disable         = no
	}

We add more serial ports to a PC like this:

/etc/rc.local:

	# assign Siig/oxford serial ports

	/bin/setserial /dev/ttyS2 port 0x4020 irq 96 uart 16950
	/bin/setserial /dev/ttyS3 port 0x4028 irq 96 uart 16950
	/bin/setserial /dev/ttyS4 port 0x40A0 irq 104 uart 16950
	/bin/setserial /dev/ttyS5 port 0x40A8 irq 104 uart 16950

Build instructions:

	su root
	cd
	cc -o c c.c
	ln -s c c0
	ln -s c c1
	ln -s c c2

Link to c for each serial port you have.  For example c2 for ttyS2.  Create
an rc file for each of these.  For example, this is for c2:

/root/.c2rc

	port = "/dev/ttyS4"
	baud = "9600"
	flow = "rtscts"
	off = "/root/power off 1"
	on = "/root/power on 1"

The parameters available in the rc file as the same as the command line
options, see below.

## For direct use on command line:

Just type:

	./c /dev/ttyS2 --baud=19200

or:

	./c /dev/ttyUSB0 --baud=9600

Here is a shell script to control CPS Power string (uses 300 baud serial
port):

	power

## Command line options

	--baud=9600	Set baud rate

	--flow=none	Set flow control: none, rtscts, or xonxoff

	--stop=1	Set number of stop bits: 1 or 2

	--bits=8	Set number of data bits: 6, 5, 7 or 8

	--parity=none	Set parity: none, even or odd

	--hup=off	Lower DTR on exit: on or off

	--cd=off	Exit if carrier detect goes low: on or off

	--on='power on 1'	Shell command to turn on power

	--off='power off 1'	Shell command to turn off power

	--help		Show this help

	--send='foo'	Send a string ot the serial port and exit

	--noansi	Suppress ANSI sequences

	--hexdump	Display received data in hexadecimal

## Commands

Hit ~h for help.  These other ~ commands are available:

	~b		Send break

	~q		Exit

	~h		Show help

	~f		Turn machine off

	~n		Turn machine on

	~!command	Execute shell command

	~<file		Send a file

	~<!command	Send command output

	~>file		Log to a file

	~>		Close log file

	~m		Modify options (such as baud rate)

	~o		Show current options

	~w		Save current options as defaults

	~s		Print status information

	~x		Toggle hex-dump mode

	~a		Toggle no-ANSI mode
