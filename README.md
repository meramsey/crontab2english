NAME

crontab2english -- explain crontab commands in English
SYNOPSIS

  Usage:
    % crontab2english [-f] files...
  Or:
    % cat files... | crontab2english
    
  If you do just this:
    % crontab2english
  then it's the same as crontab -l | crontab2english

  Example output:
  % crontab2english | less
  Setting env var MAILTO to hulahoops@polygon.int
  
  Command: (line 2)
    Run: /bin/csh -c 'perl ~/thang.pl | mail -s hujambo root'
    At: 8:10am on    the 15th of    every month
  
  Command: (line 5)
    Run: df -k
    At: 5:40am    every day
  
  Command: (line 7)
    Run: ls -l /tmp
    At: 6:50am    every Monday

  Or with the -f ("f" for filter) switch, it just adds comments
  to the input file:
  
  % crontab2english -f | less
  # My happy crontab file
  MAILTO=hulahoops@polygon.int
  10 8 15 * * /bin/csh -c 'perl ~/thang.pl | mail -s hujambo root'
  #>  At: 8:10am on    the 15th of    every month
  
  
  40 5 * * * df -k
  #>  At: 5:40am    every day
  
  50 6 * * 1 ls -l /tmp
  #>  At: 6:50am    every Monday

  % echo '10 8 15 * * foo bar%Baz Quux%Yay' | crontab2english
  Command: (line 1)
  Run: foo bar
   with input "Baz Quux\x0aYay\x0a"
  At: 8:10am on    the 15th of    every month

DESCRIPTION

It's easy to make mistakes in crontab files. Running crontab2english on your crontab files and reading the resulting English explanations will help you catch errors.
SWITCHES

-f puts this in "filter mode" -- the output is just the input plus commentary.

-v describes the current crontab2english version to STDOUT and exits.

-p forces POSIX-only mode: anything not allowed in the POSIX crontab spec won't be understood.

-e (usually default) turns off POSIX-only mode: i.e., it doesn't feign ignorance of things not in the POSIX spec.

-- signals end of switches.
ENVIRONMENT

If the evironment variables POSIXLY_CORRECT and/or POSIX_ME_HARDER are true, then this turns on -p (POSIX-only) mode by default. That's overrideable with the -e switch.
CAVEATS

I've tried to make this program understand all the kinds of crontab lines that are out there. That probably includes a few kinds of lines that your particular cron daemon doesn't understand, so just because crontab2english understands something doesn't mean your cron daemon will.

Pragmatically, however, there seem to be three kinds of cron daemons around these days:

    Ones based on old (1993ish) Vixie crontab. These understand all of POSIX, and more. This is what almost almost everyone runs these days.
    Ones that understand only what the POSIX crontab spec allows -- which excludes all sorts of amenities including: stepped ranges ("1-9/2"), "VAR=NAME" lines, English month or day abbreviations ("mon" or "jan"), day 7 meaning "Sunday", and "*/3" meaning "every third...".
    Even more recent (post-1993ish) Vixies. These seem relatively rare. They seem to be just like old Vixies, plus they understand at-words like "@annually", "@reboot", etc. (altho in some cases, those aren't mentioned in the docs!).

There could be some ancient or demented pre-Vixie non-POSIX crons running somewhere. We would hope that these would all basically understand anything that POSIX does (and possibly nothing more?), but you just might find peculiarities including:

    Rejecting 0 for Sunday, and accepting only 7.
    Rejecting 7 for Sunday, and accepting only 0.
    Accepting lists or ranges, but no lists that include ranges. I.e., allowing "7,8,9", allowing "1-3", but not allowing "1-3,7-9".
    Not accepting ranges at all?

Consult your man pages carefully.

Good general advice: keep your crontab lines simple, and that'll minimize the chances of disagreement between what you indend, what crontab2english understands (with or without -p), and what your cron daemon understands.
SEE ALSO

man 1 crontab

man 5 crontab

man 8 cron
BUG REPORTS

If this program explains a crontab line wrong, or can't parse it, then email me the line, and an explanation of how it you think it should parse.
BUGS

As of time of writing (2012-11-09), the only substantial bug that has been reported since 2001-07-29 is that in the weekday field, abbrv-abbrv things such as "tue-sat", aren't accepted. I'm now looking into this. I'm notably considering that I don't want to accept it unless most cron daemons accept it too-- especially considering the corner cases like "sat-tue" or "mon-fri/2".

Until then, using numerics gets the job done: 2-5 (which means Tuesday to Saturday).

(Pseudobugs/Harrumphs:)

The expression of i-j/y things can still kind of rub me the wrong way, no matter what way I phrase it. So I'm not sure whether "every 2nd day from Monday through Sunday" is actually improved by "every third day from Monday through Sunday". I'm also considering whether to add sanity checks for situations: "9-5" (when it's an hour field, and when it's not) "8-9/3" (every 3 days from the eighth to the ninth), and "2-5/0".
DISCLAIMER

crontab2english is distributed in the hope that it will be useful, but without any warranty; without even the implied warranty of merchantability or fitness for a particular purpose.
COPYRIGHT

Copyright 2001,2012 Sean M. Burke.

License: perlartistic ("the Perl Artistic License")
AUTHOR

Sean M. Burke, <sburke@cpan.org>
README

Translates crontab notation into English, for sanity checking: For example, "10 8 15 * * foo bar %baz" into: Run: foo bar with input "baz\x0a" At: 8:10am on the 15th of every month
SCRIPT CATEGORIES

UNIX/System_administration
CHANGE LOG

v0.72: 2012-11-09

    No changes in behavior-- just tidying up a bit of the code, adding a few comments, tweaking the docs a bit. An aside: It has been eleven years since the first version of this program. I don't know if there has been a change in how many people run what types of cron daemons (Vixie-plus-POSIX; POSIX-only, and Vixie-plus-POSIX-plus-@stuff).
v0.71: 2001-07-29

    Now supports the weird new Vixie-isms like "@annually".

    Features for feigning ignorance of non-POSIX features: -p, -e, and the evironment variables POSIXLY_CORRECT and/or POSIX_ME_HARDER.
v0.63: 2001-07-17

    Fixed a bug spotted by Greg Wimpey, where leading whitespace wasn't getting duly ignored.
v0.62: 2001-07-14

    Added special cases for when minutes field is 0.

    Added explicit "require" statement to ensure acceptable Perl version.

    Changed "Every Tuesday of May" to "Every Tuesday in May"

    Changed qr//'s to just plain strings, for all the 500404 dinosaurs.
v0.61 2001-01-23

    First public release.
