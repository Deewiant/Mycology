This is the Mycology Befunge-98 test suite, by Matti "Deewiant" Niemenmaa.

To contact me, E-mail me. The address for Mycology-related things is
matti.niemenmaa+mycology at the domain iki.fi.

Mycology is licensed under the so-called 3-clause BSD license. See license.txt
for the text of the license.

Contents of this readme:
	Recent changes
	Quick summary
	More detailed info
	Fingerprints
	Notes on particular messages
		"# across left edge"
		"101-{}"
		"4k #" and "2k ;;;5"
		"2k6"
	Old changes

Recent changes
--------------

	2010-03-18    - Don't test o if y claims it isn't supported.

For pre-2010 changes, see the full changelog at the bottom of the file.

Quick summary of how to test your Befunge interpreter:
-------------

	- If, at any point, you get messages beginning with "BAD:", correct the
	  issues before moving on to the next step.
	- If your interpreter needs any command line switches for
	  standard-conforming mode, use them.
	- Run sanity.bf and make sure it outputs "0 1 2 3 4 5 6 7 8 9 ".
	- Run mycology.b98, make sure it outputs "0 1 2 3 4 5 6 7 " and that there
	  are no lines beginning with "BAD:" anywhere in the output.
	- If your interpreter is Befunge-93 only, run mycorand.bf and examine the
	  results.
	- Run mycouser.b98: for complete testing, run it a few times and try giving
	  it both valid and invalid input.
	- If your interpreter should support one or both of the NCRS and TERM
	  fingerprints, run mycoterm.b98.
	- If your interpreter should support the TRDS fingerprint, run mycotrds.b98.

More detailed info:
-------------------

Files with the .bf extension are valid Befunge-93 source code, while *.b98 are
intended for Befunge-98 interpreters. mycology.b98 and mycouser.b98 are
exceptions: they work in both standards - if the standards are implemented
correctly.

Note for Befunge-93: mycology.b98 is much bigger than the 80x25 allowed in
Befunge-93. If your interpreter bails out on a file bigger than the maximum
allowed, you can simply take the 80x25 square starting at the top left corner
of mycology.b98 into a separate file and use that for testing.

In order to test the absolute basics of the interpreter, feed it the file
sanity.bf. This tests that the IP (instruction pointer) begins at the correct
point in Funge-Space and moves in the correct direction. In addition, it makes
sure the following instructions work:

Decimal  ASCII  Instruction

  32            Space
  35       #    Trampoline
  46       .    Output Decimal
  48       0    Push Zero
  49       1    Push One
  50       2    Push Two
  51       3    Push Three
  52       4    Push Four
  53       5    Push Five
  54       6    Push Six
  55       7    Push Seven
  56       8    Push Eight
  57       9    Push Niner
  64       @    Stop

The above are the absolute minimum which the interpreter must support. In
addition, it should reflect upon encountering an instruction it does not
recognize.

sanity.bf will, if the interpreter supports all of the above, output the string
"0 1 2 3 4 5 6 7 8 9 ". If it doesn't, anything might happen: sanity.bf does
_not_ fail safe.

Hereafter the actual testing in mycology.b98 can be conducted. The initial
behaviour of the program is to output a code, using the Output Decimal
instruction, after having successfully tested a certain instruction. These
codes are as follows:

Code  Decimal  ASCII  Instruction         Notes

 0      62       >    Go East
 1     118       v    Go South
 2      60       <    Go West
 3      94       ^    Go North
 4      36       $    Pop                 Explicitly testing whether popping an
                                          empty stack works as it should is
                                          done separately, later.

 5      34       "    Toggle Stringmode   If no reflection on the instruction
                                          occurs, but the string's contents are
                                          interpreted as instructions, a second
                                          "4 " is output before exiting.

 6      95       _    East-West If        If the comparison is done incorrectly
                                          (i.e. the wrong direction is taken)
                                          an additional "5 " is output.

 7      43       +    Add

For example, if the interpreter emits only "0 1 ", the Go East and Go South
instructions were correctly interpreted, but a reflection occurred upon meeting
Go West. A conforming interpreter should output every code in the listing once.

These tests are very simple, due to the basic nature of the instructions
involved. All that is tested is whether a reflection occurs or not. If
mycology.b98 claims that an instruction which appears to work perfectly is
failing, make sure that the above do what they should.

Having tested the above, mycology.b98 tests the Output Character instruction
and hence reverts to plain English output. If there is no output after code 7,
the Output Character instruction does not function as it should.

The output format changes to lines beginning with "BAD:" or "GOOD:", followed
by a description of what the interpreter does wrong or correctly, respectively.
Some, but not all, "BAD" lines are followed by a Stop instruction - these tend
to be features which are deemed useful enough that they are used later in the
program. (Or, possibly, features which may be used later, but aren't: it's hard
to modify Befunge source code after it's first written, so there may be some
cases where a Stop isn't necessary but is there anyway.) In some cases, a Stop
was added to simplify the code: the Befunge-93 area is particularly snarly,
since space is at a premium.

Some lines begin with "UNDEF:". This means that the specification is either
ambiguous or completely ignorant of an issue, and so different possibilities
are acceptable. It is possible that some such undefined cases may result in
"BAD:" if the interpreter does something completely unexpected, but there is no
"GOOD:" equivalent, only "UNDEF:".

Some comment lines not beginning with "BAD:", "GOOD:", or "UNDEF:" are also
emitted occasionally, in order to clarify what is going on.

Other notes on mycology.b98:

	Befunge-93 detection relies on the interpreter using only the first 80
	characters of lines, since Befunge-93 has a Funge-Space of 80x25 cells.

	The checks are generally very simple. For instance, if the Subtract or
	Multiply instructions empty the stack, they will be considered to work
	properly: only a few checks are done, and they all check for zero.

	Instructions are assumed to work if they pass one test (for the more
	complicated instructions, more tests are needed, but every behaviour of the
	instruction is still tested only once). If, for instance, an instruction
	works the first 41 times and randomly fails every third time after that,
	Mycology will probably not detect it, but will silently fail or, in the
	worst case, pass.

	Instructions are assumed to be at least somewhat sane: e.g. | should either
	reflect or pop a cell and cause the IP to start moving north or south. Not
	east or west, or to teleport to a random location in Funge- Space, or push
	72 ampersands onto the stack. It is the tester's responsibility, not
	Mycology's, to make sure that the interpreter doesn't go crazy and perform
	malicious acts. You use Mycology at your own risk.

See the end of this file for notes on particular messages.

The following instructions are _not_ tested by mycology.b98 (those preceded by
an asterisk are tested if the interpreter is detected as supporting
Befunge-98):

Decimal  ASCII  Instruction

  37       %    *Remainder
  38       &    Input Decimal
  47       /    *Divide
  61       =    Execute
  63       ?    *Go Away
 126       ~    Input Character

The division and input instructions are tested in mycouser.b98 because they all
require user intervention (the division instructions only when dividing by
zero, but I felt it would be better to not split the testing of an instruction
into two files). Their correct behaviour is also very difficult to verify
without a knowledgeable user.

Go Away is tested separately in mycorand.bf because it takes too much space to
fit in the Befunge-93 area of mycology.b98. If the Befunge-98 instruction Input
File works, mycorand.bf is loaded using it and Go Away is thus tested in
mycology.b98.

Execute is completely untested, because to get a reliable result would require
testing various commands and noting their behaviours on different platforms. An
educated guess regarding the user's platform is in order, and overall it would
be too complicated. It is simplest to test this manually, rather than to try to
cater for all cases in a suite such as Mycology.

Implementation notes regarding mycorand.bf:

	Beware! If Go Away is unimplemented and thus reflects, an infinite loop
	is entered!

	The testing is very simple: it is only made sure that Go Away causes the
	instruction pointer to go at least once in every cardinal direction,
	though it is always encountered from the same direction.

	The number of tries it took to successfully go in every direction is
	output, so an interpreter's implementer can make sure the number
	fluctuates somewhat. The order in which the directions were generated is
	also output, so that it can be verified that the order isn't always the
	same.

Make sure that the interpreter successfully passes the Befunge-93 area of
mycology.b98 before loading mycorand.bf or mycouser.bf!

Regarding fingerprints
----------------------

mycology.b98 tests every fingerprint that I am aware of, apart from FNGR, SGNL,
and WIND. It is completely up to the interpreter's writer(s) whether any should
be supported: a completely specification-conforming interpreter does not need
to support any fingerprint at all, as long as the ( "Load Semantics" and )
"Unload Semantics" instructions perform correctly.

FNGR is not tested because its specifications contradict the Befunge-98
specifications. It contains operations for performing on a single fingerprint
stack, but the specifications for Befunge-98 state that there should be a stack
of semantics for each instruction in the range [A, Z]. RC/Funge-98, the (only,
as far as I know) interpreter implementing FNGR, fails some of Mycology's tests
due to this.

SGNL is not tested simply because it is platform-specific. There is no
technical obstacle to it, only my own convictions regarding platform-specific
code. If anybody wishes to write code to test it, feel free to send it to me,
it may be worthy of addition to Mycology.

WIND is not tested because I do not wish to support it in my interpreter, and
thus I didn't feel like writing tests for it. RC/Funge-98 is the only
interpreter supporting it, and if I had discovered any bugs in it I would have
had to delve into unfamiliar code to make it even possible to test the whole
thing. I decided it was too much work and left it out. Once again, the addition
of WIND to Mycology is perfectly fine, but I won't be the one to write the
code.

The list of fingerprints which are tested:

	Official Cat's Eye Technologies fingerprints:

		"HRTI"  0x48525449  High-Resolution Timer Interface
		"MODE"  0x4d4f4445  Funge-98 Standard Modes
		"MODU"  0x4d4f4455  Modulo Arithmetic Extension
		"NULL"  0x4e554c4c  Funge-98 Null Fingerprint
		"ORTH"  0x4f525448  Orthogonal Easement Library
		"PERL"  0x5045524c  Generic Interface to the Perl Language
		"REFC"  0x52454643  Referenced Cells Extension
		"ROMA"  0x524f4d41  Funge-98 Roman Numerals
		"TOYS"  0x544f5953  Funge-98 Standard Toys
		"TURT"  0x54555254  Simple Turtle Graphics Library

	RC/Funge-98 fingerprints:

		In all cases, the documentation is considered the primary source of how
		an instruction should behave. Precise semantics have been inferred from
		the RC/Funge-98 source code where not properly documented.

		For all fingerprints involving vectors, RC/Funge-98 doesn't, for some
		reason, use the IP's storage offset. Thus, the tests assume the same
		behaviour.

		"BASE"  0x42415345  I/O for numbers in other bases
		"CPLI"  0x43504c49  Complex Integer extension
		"DATE"  0x44415445  Date functions
		"DIRF"  0x44495246  Directory functions extension
		"EVAR"  0x45564152  Environment variables extension
		"FILE"  0x46494c45  File I/O functions
		"FIXP"  0x46495850  Some useful math functions
		"FPSP"  0x46505350  Single precision floating point
		"FPDP"  0x46504450  Double precision floating point
		"FRTH"  0x46525448  Some common forth [sic] commands
		"IIPC"  0x49495043  Inter IP [sic] communicaiton [sic] extension
		"IMAP"  0x494d4150  Instruction remap extension
		"INDV"  0x494e4456  Pointer functions
		"SOCK"  0x534f434b  tcp/ip [sic] socket extension
		"STRN"  0x5354524e  String functions
		"SUBR"  0x53554252  Subroutine extension
		"TERM"  0x5445524d  Terminal control functions
		"TIME"  0x54494d45  Time and Date functions
		"TRDS"  0x54524453  IP travel in time and space

	Jesse van Herk's extensions to RC/Funge-98:

		"JSTR"  0x4a535452
		"NCRS"  0x4e435253  Ncurses [sic] extension

	GLFunge98 fingerprints:

		"SCKE"  0x53434b45

Notes on particular messages output by mycology.b98
---------------------------------------------------

"UNDEF: # across left edge..."
..............................

Here, the line and file cases are considered separately. This is because some
interpreters consider the Funge-Space as a rectangle: see below, using 0 to
mark empty cells which are outside Funge-Space.

>    v000
v    >  >
@00000000

Even though the file doesn't contain the three spaces at the end of the first
line, or any of the spaces at the end of the third line, the program's
representation of Funge-Space does, because Funge-Space is padded out to the
width of the longest line in the file.

Because jumping across the edge of Funge-Space isn't mentioned in the
specification, one cannot be sure as to what should happen. If it is considered
that Funge-Space is infinitely surrounded by spaces, jumping across the edge of
space shouldn't skip over anything. On the other hand, # jumps over "the next
Funge-Space cell in [the instruction pointer's] path", which might not include
this void.

However, it may be that an existing space cell which is not part of this void
is skipped over. Thus, both jumping over the edge of the physical edge of the
file, with only the void in between, and jumping over the edge of a line which
is shorter, but may contain the spaces as the 0s in the above example, are
tested. Most interpreters have different behaviour for the two.

"BAD: 101-{} doesn't leave stack top as 0 and next as 1"
........................................................

This is something which may be tricky to get right. Let's examine what happens.
On each following line, the instruction comes first, followed by the stack
stack, with the contents of each stack in square brackets, starting at the
bottom.

1 [1]
0 [1, 0]
1 [1, 0, 1]
- [1, -1]

This part is trivial. What happens next, however, varies.

One incorrect possibility:
{ [1, 0, 0], []
} [1]

Here, { pushes abs(-1) zeroes onto the SOSS before a new stack is pushed. Since
there was no SOSS at that time, the zero pushed doesn't appear.

This is the behaviour of the Flaming Bovine Befunge Interpreter version
2003.0326, amended with the 2003.0722 and 2003.0726 patches.

Another:
{ [1, 0, 0], [0]
} [1]

It seems that here, abs(-1) zeroes are being pushed on the TOSS instead of the
SOSS.

This is the behaviour of the RC/Funge-98 interpreter, version 1.07, as well as
of the RC/Funge-98 interpreter modified by Jesse van Herk, version 1.05.

What should happen:
{ [1, 0, 0, 0], []
} [1, 0]

This is the behaviour of the Conforming Concurrent Befunge-98 Interpreter,
version 1.00.

Let's see what the spec has to say about the subject:

	"The { 'Begin Block' instruction pops a cell it calls n, then pushes a new
	stack on the top of the stack stack, transfers n elements from the SOSS to
	the TOSS, then pushes the storage offset as a vector onto the SOSS..."

	"If n is negative, |n| zeroes are pushed onto the SOSS."

In other words, { should:

	Pop the -1 from the stack.                      [1]
	Push a new stack on the stack stack.            [1], []
	Since -1 < 0, push |-1| = 1 zero onto the SOSS. [1, 0], []
	Push the storage offset onto the SOSS.          [1, 0, 0, 0], []

"BAD: 4k #..." and "BAD: 2k ;;;5..."
....................................

In Funge-98, spaces and semicolons are ethereal. The "next instruction"
mentioned in the spec refers specifically to the next instruction the
interpreter would execute if the k would not be there.

Hence, k always executes its operand at the k, but reaches past all spaces and
semicolons to find the operand. Hence 2k ;;;5 should execute the 5 twice at the
k. (See the next section for the reason why it should be executed a third time
afterward.)

"BAD: 2k6..."
.............

The specification does not say that the operand should be skipped over after
execution. The only special case is when the amount of times to execute is
zero.

This means that 2k6 should indeed first push 2 sixes at the k, and then a third
when encountering the 6 itself.

This also means that there is no way to execute an instruction only once: 1k6
results in two sixes. (Another IP may certainly modify the 6 immediately after
the k is executed, but that's a somewhat unlikely case and not exactly a good
way to handle this limitation.)

The spec is somewhat unclear on the entirety of k, but both of the above issues
have been confirmed with Chris Pressey.

Old changes
-----------

	2009-05-13    - MycoTRDS accepts P-values greater than 0 and reports
	                unacceptable values as UNDEF, not BAD.
	2009-04-04    - Made the u test not abort if the storage offset isn't (0,0).
	              - Bugfix: u outputs the correct error message if it fails with
	                a positive count.
	2009-03-31    - Bugfix: some w were misaligned in the TOYS test.
	2009-03-29    - Bugfix: 1y bits testing was really broken, really fixed it
	                now.
	2009-03-28    - Update: FILE's R really should reflect at EOF.
	              - Update: removed the 'G to an infinite loop' test from STRN,
	                        it makes sense that it does indeed loop forever.
	              - Bugfix: it was always claimed that I/O was buffered.
	              - Bugfix: 1y being greater than 15 was complained about:
	                        should have been 31.
	2008-11-15    - ) with a negative count wasn't actually tested, ( was used
	                both times.
	2008-10-17    - If o doesn't work, it is reported that i in binary mode will
	                not be tested.
	2008-09-21    - Fixed a misalignment in the fingerprint loading code.
	2008-09-16    - MycoTRDS now expects ticks to start from zero, thus the
	                expected value of G is one lower.
	2008-09-15    - Fixed a misalignment in the u test with a negative argument.
	2008-09-14    - Made the wraparound with non-cardinal delta test catch a
	                common case.
	2008-09-13    - Bugfix: test for k with negative argument was expecting
	                incorrect k behaviour.
	              - Bugfix: "GOOD: SGML spaces" was never output, who knows for
	                how long that's been disabled.
	              - Bugfix: IMAP check for non-ASCII now says it works when it
	                works.
	              - Update: IMAP check for non-ASCII is now GOOD when it works
	                and BAD otherwise, per the latest spec.
	              - Update: IMAP now checks mappings outside range 0-255.
	              - Update: INDV now expects the logical order, reporting BAD
	                otherwise.
	2008-09-12    - Reduce stacking in HRTI test.
	              - Corrected typo in a TOYS error message.
	2008-09-10    - Made the check for wraparound with non-cardinal delta a bit
	                stricter (instead of a delta of (12,0) it uses (13,2)).
	2008-09-06    - Fixed a misspelled error message in mycouser.b98 for
	                Befunge-93 interpreters.
	              - Fixed a bug in 2k6 testing that led to an infinite loop.
	2008-08-30    - Fixed the case where SCKE is included in SOCK.
	2008-08-28    - SOCK and SCKE fixed: much code still assumed that A
	                overwrites the original socket, and thus wrong sockets were
	                being given to K and P.
	2008-08-20    - Bugfix: results for the ;; concurrency test were off by one.
	              - Test new A and O instructions in SUBR.
	2008-08-19    - Bugfix: results for the concurrency tests 5kz and "a  b"
	                were incorrect.
	2008-08-14    - New fingerprint: DATE.
	2008-08-13    - I had managed to get the way y should work as a pick
	                instruction completely wrong. Thanks to Johannes Laire for
	                noticing this and notifying me.
	2008-08-11    - Removed PNTR (the same as INDV), it wasn't meant to exist
	                any more.
	2008-08-09    - The new addition to the FILE fingerprint, D, is now tested.
	              - Using it, created .tmp files can now be removed from within
	                Mycology.
	2008-07-26    - Thanks to Arvid Norlander, Chris Pressey, and Mike Riley,
	                none of k is UNDEF any longer, and some tests were changed
	                to reflect the intended behaviour.
	              - Expanded the null byte test.
	              - Bugfix: in SOCK, the original socket should /not/ be
	                destroyed: flipped a GOOD and BAD.
	2008-07-19    - Now testing whether null bytes are handled correctly.
	2008-05-02    - Bugfix: mycouser.b98 had a forgotten r in place of a (.
	2008-03-30    - Bugfix: J test in SUBR was misaligned.
	2008-03-29    - Bugfix: D failing in TOYS had no error message.
	              - Bugfex: L and R in TOYS had incorrect error messages.
	2008-03-15    - Bugfix: time output for hours <= 10 was incorrect.
	2008-03-13    - Bugfix: a missing ; caused an incorrect error message.
	2008-03-11    - i and o are now UNDEF if unavailable.
	              - PERL is now tested with "5-1" instead of the palindromic
	                "2+2". Thanks to Alex Smith for the input.
	2008-02-02    - 1k # now considered UNDEF.
	2008-01-09    - More typos or incorrect messages.
	2007-12-02    - Corrected some typos.
	2007-09-22    - Minor bugfixes.
	2007-09-20    - Public release.
	2007-07-26    - Creation of mycoterm.b98 and mycotrds.b98.
	2007-06-17    - Creation of mycouser.b98.
	2007-01-06    - Creation of mycorand.bf.
	2006-12-31    - Creation of sanity.bf and mycology.b98.
