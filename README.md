# fplll 4.0 #

* [Overall description](#overall_description)
* [Dependencies](#dependencies)
* [Installation](#installation)
* [Check](#check)
* [How to use](#how_to_use)
	* [latticegen](#latticegen)
	* [fplll](#fplll)
	* [llldiff](#llldiff)
* [How to use as a library](#how_to_use_as_a_library)
	* [Functions](#functions)
		* [LLL](#lll)
		* [BKZ](#bkz)
		* [SVP](#svp)
	* [Data Types](#data_types)
		* [Z\_NR](#z_nr)
		* [FP\_NR](#fp_nr)
		* [Matrix](#matrix)
		* [MatrixRow](#matrix_row)
		* [ZZ\_mat](#zz_mat)
		* [FP\_mat](#fp_mat)
		* [See also](#see_also)
* [Expamles](#examples)
* [Developers](#developers)
* [How to cite](#how_to_cite)
* [Acknowledgments](#acknowledgments)
* [New releases and bug reports](#new_releases_and_bug_reports)

<a name="overall_description"></a>
## Overall description ##

fplll is distributed under the [GNU Lesser General Public License](COPYING) (either version 2.1 of
the License, or, at your option, any later version) as published by the Free Software Foundation.

fplll contains several algorithms on lattices that rely on floating-point computations. This
includes implementations of the floating-point LLL reduction algorithm, offering different
speed/guarantees ratios. It contains a 'wrapper' choosing the estimated best sequence of variants in
order to provide a guaranteed output as fast as possible. In the case of the wrapper, the succession
of variants is oblivious to the user. It also includes a rigorous floating-point implementation of
the Kannan-Fincke-Pohst algorithm that finds a shortest non-zero lattice vector, and the BKZ
reduction algorithm.

<a name="dependencies"></a>
## Dependencies ##

* Absolutely needed:
	* GNU MP 4.2.0 or higher [http://gmplib.org/](http://gmplib.org/)
	* MPFR 2.3.0 or higher, COMPLETE INSTALLATION [http://www.mpfr.org/](http://www.mpfr.org/)
	* autotools 2.61 or higher
* Optional:
  * QD 2.3.15 or higher (a C++/Fortran-90 double-double and quad-double package), compile and install the shared library (e.g ./configure --enable-shared=yes). [http://crd-legacy.lbl.gov/~dhbailey/mpdist/]

If GMP and/or MPFR include and lib files are not in the default
directories /usr/include and /usr/lib, you have to set the
environment variables CFLAGS and LDFLAGS for instance through
the configure command line

```./configure CPPFLAGS="-I/mpfrinclude -I/gmpinclude" LDFLAGS="-L/mpfrlib -L/gmplib"```

or

```./configure CPPFLAGS="-I/mpfrinclude -I/gmpinclude $CPPFLAGD" LDFLAGS="-L/mpfrlib -L/gmplib $LDFLAGS"```

if these variables already exist in your environment.  This should be modified soon for using
standard `--with-gmp` and `--with-mpfr` package specifications. The same philosophy applies to the (optional) QD library. 

<a name="installation"></a>
## Installation ##

You should downloaded the source code from github and then run

    ./autogen.sh

which generates the `./configure` script used to configure fplll by calling the appropriate
autotools command.

Then, to compile and install type

	./configure
	make
	make install			# (as root)

You can remove the program binaries and object files from the source code directory by typing `make
clean`. To also remove the files that `./configure` created (so you can compile the package for a
different kind of computer), type `make distclean`.  By default, `make install` installs the package
commands under `/usr/local/bin`, include files under `/usr/local/include`, etc.  You can specify an
installation directory name other than `/usr/local` by giving `./configure` the option
`--prefix=dirname`.  Run `./configure --help` for further details.

<a name="check"></a>
## Check ##

'cd' to the src directory, and type

	make check

This tests the LLL wrapper given dim55_in as input. If the error message
'INVALID RESULT' is not printed, the self-test has succeeded.

<a name="how_to_use"></a>
## How to use ##

Executable files fplll and latticegen are installed in the directory
bin. If you type 'make check', it will also generate the file llldiff.
(Note that the programs generated by make in the 'src/' directory are
only wrappers to the programs in 'src/.libs/)

<a name="latticegen"></a>
### latticegen ###

latticegen is an utility for generating matrices (rows form input
lattice bases).

The options are :

* r `d` `b` : generates a knapsack style matrix of dimension d,d+1 and b bits.
* s `d` `b` `b2` : generates a simdioph matrix.
* u `d` `b` : generates an uniform matrix.
* n `d` `b` `q` : generates an ntru like matrix.
* N `d` `b` `q` : generates an ntru like matrix.
* a `d` `f` : generates an ajtai style matrix.
* A `d` : generates an ajtai style matrix. Also requires d coefficients.

The matrix is printed in stdout.

Note that by default, the random bits always use the same seed,
to ensure reproducibility. You can change the seed with the option
``` -randseed <integer>``` or use the current time (in seconds)
``` -randseed time```

If you use this option, it must be the first one on the command line.


<a name="fplll"></a>
### fplll ###

fplll does LLL, BKZ or SVP on a matrix (considered as a set of row
vectors) given in stdin or in a file as parameter.

The options are:

* -a lll : LLL-reduction (default).
* -a bkz : BKZ-reduction.
* -a svp : print a shortest vector of the lattice.
* -r `size`, -c `size` : ignored, provided for compatibility with previous versions of fplll.

Options for LLL-reduction:

* -d `delta` :     delta (default=0.99)
* -e `eta` :       eta (default=0.51)
* -l `lovasz` :    if !=0 Lovasz's condition. Otherwise, Siegel's condition (default: Lovasz)
* -p `precision` : precision of the floating-point arithmetic, works only with -f mpfr.

* -f mpfr : sets the floating-point type to MPFR (default if m=proved).
* -f dd : sets the floating-point type to double-double.
* -f qd : sets the floating-point type to quad-double.
* -f dpe : sets the floating-point type to DPE (default if m=heuristic/heuristicearly).
* -f double : sets the floating-point type to double (default if m=fast/fastearly).
* -f longdouble : sets the floating-point type to long double.

* -z int : sets the integer type to int.
* -z mpz : sets the integer type to mpz, the integer type of GMP (default).
* -z double : sets the integer type to double.

* -m wrapper : uses the wrapper. (default if z=mpz)
* -m fast : uses the fast method, works only with -f double.
* -m fastearly : uses the fast method with early reduction, works only with -f double.
* -m heuristic : uses the heuristic method.
* -m heuristicearly : uses the heuristic method with early reduction.
* -m proved : uses the proved version of the algorithm.

With the wrapper or the proved version, it is guaranteed that the basis is
LLL-reduced with delta'=2&times;delta-1 and eta'=2&times;eta-1/2. For instance, with the
default options, it is guaranteed that the basis is (0.98,0.52)-LLL-reduced.

Options for BKZ-reduction:

* -b `blocksize`            Block size, mandatory, between 2 and the number of rows.
* -f `float_type`           Same as LLL (-p is required if float_type=mpfr)
* -p `precision`            Precision of the floating-point arithmetic with -f mpfr
* -bkzmaxloops `loops`      Maximum number of full loops.
* -bkzmaxtime `time`        Stop after <i>time</i> seconds (up to loop completion).
* -bkzautoabort                   Heuristic, stop when the average slope of log(||b_i*||) does not decrease fast enough.
* -bpre `blocksize`         Pre-processing block size. Between 2 and the block size.
* -bkzlinearpruning `level` Enables linear pruning in enumeration, such that the last level steps drop with slope -1/blockSize
* -bkzdumgso `file_name`    Dumps the log(||b_i*||)'s in specified file.

<a name="llldiff"></a>
### llldiff ###

llldiff compares two bases (b1,...,bd) and (c1,...c_d'): they are considered
equal iff d=d' and for any i, bi = +- ci.

<a name="how_to_use_as_a_library"></a>
## How to use as a library ##

At the beginning of your code, type:

	#include <fplll.h>
	using namespace fplll;

See the example file test.cpp in the src directory.
To compile it, assuming fplll has been installed in /tmp/fplll:

	bash-3.00$ g++ -static -I /tmp/fplll/include test.cpp -L /tmp/fplll/lib -lfplll -lmpfr -lgmp
	bash-3.00$ ./a.out
	[[4 3 7]
	[3 0 1]
	[3 5 3]]
	[[3 0 1]
	[2 2 -3]
	[0 5 2]]

All types, functions and constants are wrapped in the ```fplll``` namespace,
with the exception of the ```dpe``` type defined in dpe.h. Preprocessor
definitions prefixed by FPLLL_ are reserved for internal use.

<a name="functions"></a>
### Functions ###


<a name="lll"></a>
#### LLL ####

<tr><td><tt>int lllReduction(ZZ\_mat&lt;mpz\_t&gt;&amp; b, double delta =
0.99, double eta = 0.51, LLLMethod method = LM\_WRAPPER,
FloatType floatType = FT\_DEFAULT, int precision = 0, int flags =
LLL\_DEFAULT)</tt><br>
<p>LLL-reduces a basis of Z_NR&lt;mpz_t&gt;.</p>
<p>It is guaranteed that the output is (delta', eta')-LLL-reduced with delta'=2&times;delta-1, eta'=2&times;eta-1/2 provided that
method=LM_WRAPPER/LM_PROVED, floatType=FT_DEFAULT and precision=0. For instance, with the default parameters, it is
guaranteed that the output basis is (0.98, 0.52)-LLL-reduced.</p>
<p>Parameters:</p>
<blockquote>
  <dl>
    <dt><code>b</code>
      <dd>Input basis. It is reduced in place.</dd>
    <dt><code>delta</code>
      <dd>Relaxation factor in the Lov&aacute;sz condition. Must be greater than 1/4 and lower than 1.
    <dt><code>eta</code>
      <dd>Relaxation factor in the size reduction. Must be greater that 1/2 and lower than sqrt(delta).
    <dt><code>method</code>
      <dd>One of the following values:
      <table cellpadding="3">
        <tr><td>LM_WRAPPER<td>Tries to reduce the matrix with a combination of the following methods with increasing precision. Then, runs the proved version with the default precision. floatType must be FT_DEFAULT and precision must be 0. The result is guaranteed (see above).
        <tr><td>LM_PROVED<td>Proved method. Uses integers to compute dot products. The result is guaranteed if floatType=FT_DEFAULT/FT_MPFR and precision=0 (see above).
        <tr><td>LM_HEURISTIC<td>Heuristic method. Uses floating-point numbers to compute dot products. The result is not guaranteed. It is more efficient that the proved version when the coefficients of the input are large (the threshold depends on the floating-point type and precision).
        <tr><td>LM_FAST<td>Same as LM_HEURISTIC with floatType=FT_DOUBLE, with a special trick to handle larger inputs.
      </table>
    <dt><code>floatType</code>
      <dd>Possibles values are:
      <table cellpadding="3" border style="border-collapse:collapse;">
      <tr><td><td>LM_WRAPPER<td>LM_PROVED<td>LM_HEURISTIC<td>LM_FAST
      <tr><td>FT_DEFAULT<td>yes<td>yes<br>same as FT_MPFR<td>yes<br>same as FT_DPE<td>yes<br>same as FT_DOUBLE
      <tr><td>FT_DOUBLE<td>no<td>yes<td>yes<td>yes
      <tr><td>FT_DD<td>no<td>yes<td>yes<td>no
      <tr><td>FT_QD<td>no<td>yes<td>yes<td>no
      <tr><td>FT_DPE<td>no<td>yes<td>yes<td>no
      <tr><td>FT_MPFR<td>no<td>yes<td>yes<td>no
      </table>
    <dt><code>precision</code>
      <dd>If floatType is not FP_MPFR, this parameter must be zero. If floatType is FP_MPFR,
      <ul>
      <li>if this parameter is zero, the precision of floating-point computation is the one required to ensure that the proved method returns a correct result (note that if the heuristic method is used with this precision, nothing is guaranteed);
      <li>if this parameter is larger than or equal to 53, it forces the precision in floating-point computations.
      </ul>
    <dt><code>flags</code>
      <dd>Can be LLL_DEFAULT or a combination of the following values:
      <table cellpadding="3">
      <tr><td>LLL_VERBOSE<td>Displays information on stderr.
      <tr><td>LLL_EARLY_RED<td>Enables early reduction. This might be faster on (nearly) lower triangular matrices. Currently, this flag is not compatible with the proved method.
      <tr><td>LLL_SIEGEL<td>Uses Siegel condition instead of Lov&aacute;sz condition.
      </table>
  </dl>
</blockquote>

Return value:
<blockquote>
<table cellpadding="3">
<tr><td>RED_SUCCESS<td>Success.
<tr><td>RED_BABAI_FAILURE<td>Error.
<tr><td>RED_LLL_FAILURE<td>Error: infinite loop in LLL.
<tr><td>Any other value<td>Error.
</table>
Even if an error occurs, it is guaranteed that <code>b</code> remains a basis of the same lattice.
</blockquote>

<tr><td><tt>int lllReduction(ZZ\_mat&lt;long&gt;&amp; b, double delta =
0.99, double eta = 0.51, LLLMethod method = LM\_FAST,
FloatType floatType = FT\_DEFAULT, int precision = 0, int flags =
LLL\_DEFAULT)</tt>

LLL-reduces a basis of Z\_NR&lt;long&gt;. There is no guarantee and the LM\_WRAPPER method is not available.

<tr><td><tt>int lllReduction(ZZ\_mat&lt;double&gt;&amp; b, double delta =
0.99, double eta = 0.51, LLLMethod method = LM\_FAST,
FloatType floatType = FT\_DEFAULT, int precision = 0, int flags =
LLL\_DEFAULT)</tt>

LLL-reduces a basis of Z\_NR&lt;double&gt;. There is no guarantee and the LM\_WRAPPER method is not available.


<a name="bkz"></a>
#### BKZ ####

<table cellpadding="5">
<tr><td><tt>int bkzReduction(IntMatrix&amp; b, int blockSize, int flags = BKZ_DEFAULT)
</tt><br>
<p>BKZ-reduces a basis of Integers.</p>
<p>Parameters:</p>
<blockquote>
  <dl>
    <dt><code>b</code>
      <dd>Input basis. It is reduced in place.</dd>
    <dt><code>blockSize</code>
      <dd>Controls the strength of the reduction (as low as LLL if blockSize=2, as high as HKZ when blockSize=b.getRows()).
    <dt><code>flags</code>
      <dd>Can be BKZ_DEFAULT or a combination of the following values:
      <table cellpadding="3">
      <tr><td>BKZ_VERBOSE<td>Displays information on stderr.
      <tr><td>BKZ_NO_LLL<td>Assumes that the input basis is already LLL-reduced (otherwise, an LLL-reduction is performed with the LLL wrapper to avoid numerical problems)
      <!-- <tr><td>BKZ_AUTO_ABORT<td> -->
      </table>
  </dl>
</blockquote>
<p>Return value:</p>
<blockquote>
  <table cellpadding="3">
  <tr><td>RED_SUCCESS<td>Success.
  <tr><td>RED_BABAI_FAILURE<td>Error in the semi-reduction subroutine.
  <tr><td>RED_LLL_FAILURE<td>Error: infinite loop in the LLL subroutine.
  <tr><td>RED_ENUM_FAILURE<td>Error in the SVP subroutine.
  <tr><td>RED_BKZ_FAILURE<td>Error in BKZ.
  <tr><td>RED_BKZ_TIME_LIMIT<td>Time limit exceeded in BKZ.
  <tr><td>RED_BKZ_LOOPS_LIMIT<td>Maximum number of loops exceeded in BKZ.
  <tr><td>Any other value<td>Error.
  </table>
  <p>Even if an error occurs, it is guaranteed that <code>b</code> remains a basis of the same lattice.</p>
</blockquote>
<tr><td><tt>int bkzReduction(IntMatrix&amp; b, IntMatrix&amp; u, int blockSize, int flags = BKZ_DEFAULT)</tt><br>
Same as above, but also computes the transform matrix u such that b<sub>new</sub> = u &times; b<sub>old</sub>.
<tr><td><tt>int bkzReduction(const BKZParam&amp; param)</tt><br>
<p>Same as above with more options.</p>
<p>Fields of BKZParam:</p>
<blockquote>
  <pre>
  struct BKZParam {
    BKZParam() : b(NULL), u(NULL), blockSize(0), delta(LLL_DEF_DELTA),
      floatType(FT_DEFAULT), precision(0), flags(BKZ_DEFAULT),
      maxLoops(0), maxTime(0) {}
    IntMatrix* b;
    IntMatrix* u;
    int blockSize;
    double delta;
    FloatType floatType;
    int precision;
    int flags;
    int maxLoops;
    double maxTime;
    vector&lt;double&gt; pruning;
  };
  </pre>
  <dl>
    <dt><code>b</code>
      <dd>Pointer to the matrix to reduce in place.</dd>
    <dt><code>u</code>
      <dd>Pointer to the transform matrix (can be null if not needed).</dd>
    <dt><code>blockSize</code>
      <dd>Between 2 and b.getRows(), controls the strength of the reduction.
    <dt><code>delta</code>
      <dd>Used by the LLL subroutine.
    <dt><code>floatType</code>
      <dd>Internal data type for floating-point computations (FT_DEFAULT, FT_DOUBLE, FT_LONG_DOUBLE, FT_DPE, FT_DD, FT_QD or FT_MPFR). FT_DEFAULT is currently equivalent to FT_DOUBLE.
    <dt><code>precision</code>
      <dd>Internal precision of floating-point computations when floatType = FT_MPFR.
    <dt><code>flags</code>
      <dd>Can be BKZ_DEFAULT or a combination of the following values:
      <table cellpadding="3">
      <tr><td>BKZ_VERBOSE<td>Displays information on stderr.
      <tr><td>BKZ_NO_LLL<td>Assumes that the input basis is already LLL-reduced (otherwise, an LLL-reduction is performed with the LLL wrapper to avoid numerical problems)
      <tr><td>BKZ_MAX_LOOPS<td>Enable parameter maxLoops.
      <tr><td>BKZ_MAX_TIME<td>Enable parameter maxTime.
      <!-- <tr><td>BKZ_AUTO_ABORT<td> -->
      </table>
    <dt><code>maxLoops</code>
      <dd>Forced stop after <i>maxLoops</i> loops (enabled by the BKZ_MAX_LOOPS flag).
    <dt><code>maxTime</code>
      <dd>Forced stop after around maxTime seconds (enabled by the BKZ_MAX_TIME flag, the condition is checked only after each full loop).
    <dt><code>pruning</code>
      <dd>Vector of size <i>blockSize</i> that enables heuristic speed-up of the enumeration subroutine.
        If not empty, it must contain <i>blockSize</i> values in the interval (0,1], starting with 1, in non-increasing order.
  </dl>
</blockquote>
<p>Return value: Same as above.</p>
</table>

<a name="svp"></a>
#### SVP ####

<table>
<tr><td><tt>int shortestVector(IntMatrix&amp; b, vector&lt;Integer&gt;&amp;
solCoord, SVPMethod method = SVPM_PROVED, int flags = SVP_DEFAULT)</tt><br>
<p>Computes a shortest non-zero vector of a lattice.
The basis must be LLL-reduced with delta = 0.99 and eta = 0.51.
The result is guaranteed if method = SVPM_PROVED.</p>
<p>Parameters</p>
<blockquote>
  <dl>
    <dt>b
      <dd>LLL-reduced input basis.</dd>
    <dt>solCoord
      <dd>Output: coordinates of a shortest vector non-zero of L(b) in the basis <code>b</code>.
    <dt>method
      <dd>SVPM_PROVED (the result is guaranteed provided that the basis is (0.99,0.51)-LLL-reduced) or SVPM_FAST (nothing is guaranteed).
    <dt>flags
      <dd>SVP_DEFAULT or SVP_VERBOSE (displays information on stderr).
  </dl>
</blockquote>
<p>Return value:</p>
<blockquote>
  <table cellpadding="3">
  <tr><td>RED_SUCCESS<td>Success.
  <tr><td>RED_ENUM_FAILURE<td>Error: no solution found.
  <tr><td>Any other value<td>Error.
  </table>
</blockquote>
</table>

<a name="data_types"></a>
### Data Types ###

<a name="z_nr"></a>
#### Z\_NR&lt;Z&gt; ####

Z\_NR stores integers. This template provides a uniform interface for doing
integer computations with several underlying types (long, double and
mpz\_t).

Methods:

<table cellpadding="5">
<tr><td><tt>Z_NR()</tt><br>
Default constructor. The initial value is undefined.
<tr><td><tt>Z_NR(const Z_NR&lt;Z&gt;&amp; x)</tt><br>
Copy constructor.
<tr><td><tt>~Z_NR&lt;Z&gt;()</tt><br>
Destructor.
<tr><td><tt>double get_d() const</tt><br>
Converts this object to a double. If it does not fit in a double, the result is undefined.
<tr><td><tt>double get_d_2exp(long* expo) const</tt><br>
Computes expo such value 2^(expo-1) &lt;= value &lt; 2^expo and returns
value / 2^expo. This means that expo = floor(log2(value)) - 1. If the value of
this object is zero, returns 0 and sets expo = 0.
<tr><td><tt>long get_si() const</tt><br>
Converts this object to a long. If it does not fit in a long, the result is undefined.
<tr><td><tt>template&lt;class F&gt; void set_f(const FP_NR&lt;F&gt;&amp; x)</tt><br>
Sets the value to x. When F=mpfr_t, x is rounded to the nearest integer and if
the fractional part of x is 0.5, the even integer is chosen when. Otherwise,
the rounding direction is undefined.
<tr><td><tt>void set_str(const char* s)</tt><br>
Sets the value to s, signed integer in basis 10.
<tr><td><tt>void operator=(const Z_NR&lt;Z&gt;&amp; x)</tt><br>
<tt>void operator=(long x)</tt><br>
Sets the value to x.
<tr><td><tt>int cmp(const Z_NR&lt;Z&gt;&amp; x) const</tt><br>
3-way comparison. Returns a positive number if *this > x, a negative number if
*this &lt; x or zero is *this == x.
<tr><td><tt>int sgn() const</tt><br>
Sign. Returns a positive number, a negative number or zero if the value of
this object is respectively positive, negative or null.
<tr><td><tt>inline bool operator&lt;(const Z_NR&lt;Z&gt;&amp; x) const</tt><br>
<tt>inline bool operator&gt;(const Z_NR&lt;Z&gt;&amp; x) const</tt><br>
<tt>inline bool operator&lt;=(const Z_NR&lt;Z&gt;&amp; x) const</tt><br>
<tt>inline bool operator&gt;=(const Z_NR&lt;Z&gt;&amp; x) const</tt><br>
<tt>inline bool operator==(const Z_NR&lt;Z&gt;&amp; x) const</tt><br>
<tt>inline bool operator!=(const Z_NR&lt;Z&gt;&amp; x) const</tt><br>
<tt>inline bool operator&lt;(long x) const</tt><br>
<tt>inline bool operator&gt;(long x) const</tt><br>
<tt>inline bool operator&lt;=(long x) const</tt><br>
<tt>inline bool operator&gt;=(long x) const</tt><br>
<tt>inline bool operator==(long x) const</tt><br>
<tt>inline bool operator!=(long x) const</tt><br>
Comparison operators.
<tr><td><tt>void add(const Z_NR&lt;Z&gt;&amp; x, const Z_NR&lt;Z&gt;&amp; y)<br>
void sub(const Z_NR&lt;Z&gt;&amp; x, const Z_NR&lt;Z&gt;&amp; y)<br>
<!--void neg(const Z_NR&lt;Z&gt;&amp; x)<br>-->
void mul(const Z_NR&lt;Z&gt;&amp; x, const Z_NR&lt;Z&gt;&amp; y)<br>
void mul_si(const Z_NR&lt;Z&gt;&amp; x, long y);<br>
void abs(const Z_NR&lt;Z&gt;&amp; x)</tt><br>
Sets the value of this object to x + y, x - y<!--, -x-->, x &times; y, x &times; y,
or |x| (respectively).
<tr><td><tt>void addmul(const Z_NR&lt;Z&gt;&amp; x, const Z_NR&lt;Z&gt;&amp; y)</tt><br>
Adds x &times; y to the current value.
<tr><td><tt>void submul(const Z_NR&lt;Z&gt;&amp; x, const Z_NR&lt;Z&gt;&amp; y)</tt><br>
Subtracts x &times; y from the current value.
<tr><td><tt>void swap(Z_NR&lt;Z&gt;&amp; a)</tt><br>
Efficiently swaps the values of two Z_NR.
<tr><td><tt>T&amp; getData()</tt><br>
<tt>const T&amp; getData() const</tt><br>
Returns the internal representation of the data.
</table>

Non-member functions:

<table cellpadding="5">
<tr><td><tt>template &lt;class Z&gt;<br>ostream&amp; operator&lt;&lt;(ostream&amp; os, const Z_NR&lt;Z&gt;&amp; x)</tt><br>
Prints x on stream <code>os</code>.
<tr><td><tt>template &lt;class Z&gt;<br>istream&amp; operator&gt;&gt;(istream&amp; is, Z_NR&lt;Z&gt;&amp; x)</tt><br>
Reads x from stream <code>is</code>.
</table>

Containers:

<tt>typedef Z\_NR&lt;mpz\_t&gt; Integer;</tt><br>
<tt>typedef std::vector&lt;Integer&gt; IntVect;<br>
typedef ZZ\_mat&lt;mpz_t&gt; IntMatrix;</tt>

<a name="fp_nr"></a>
#### FP\_NR&lt;F&gt; ####

FP\_NR stores floating-point numbers. This template provides a uniform
interface for doing floating-point computations with several underlying
types (double, dpe_t, dd, qd and mpfr\_t). For all functions, the rounding mode rnd is ignored unless F=mpfr\_t.

Methods:

<table cellpadding="5">
<tr><td><tt>FP_NR()</tt><br>
Default constructor. The initial value is undefined.
<tr><td><tt>FP_NR(const FP_NR&lt;F&gt;&amp; x)</tt><br>
Copy constructor.
<tr><td><tt>~FP_NR&lt;F&gt;()</tt><br>
Destructor.
<tr><td><tt>double get_d() const</tt><br>
Converts this object to a double. If it does not fit in a double, the result is
undefined.
<tr><td><tt>long get_si() const</tt><br>
Converts this object to a long. The rounding direction is undefined. If it does
not fit in a long, the result is undefined.
<tr><td><tt>template&lt;class Z&gt; void set_z(const Z_NR&lt;Z&gt;&amp; x, mp_rnd_t rnd = GMP_RNDN)</tt><br>
Sets the value to x.
<tr><td><tt>void operator=(const FP_NR&lt;F&gt;&amp; x)</tt><br>
<tt>void operator=(double x)</tt><br>
Sets the value to x.
<tr><td><tt>int cmp(const FP_NR&lt;F&gt;&amp; x) const</tt><br>
<tt>int cmp(double x) const</tt><br>
3-way comparison. Returns a positive number if *this > x, a negative number if *this &lt; x or zero is *this == x.
<tr><td><tt>int sgn() const</tt><br>
Sign. Returns a positive number, a negative number or zero if the value of this
object is respectively positive, negative or null.
<tr><td><tt>inline bool operator&lt;(const FP_NR&lt;F&gt;&amp; x) const</tt><br>
<tt>inline bool operator&gt;(const FP_NR&lt;F&gt;&amp; x) const</tt><br>
<tt>inline bool operator&lt;=(const FP_NR&lt;F&gt;&amp; x) const</tt><br>
<tt>inline bool operator&gt;=(const FP_NR&lt;F&gt;&amp; x) const</tt><br>
<tt>inline bool operator&lt;(double x) const</tt><br>
<tt>inline bool operator&gt;(double x) const</tt><br>
<tt>inline bool operator&lt;=(double x) const</tt><br>
<tt>inline bool operator&gt;=(double x) const</tt><br>
Comparison operators.
<tr><td><tt>void add(const FP_NR&lt;F&gt;&amp; x, const FP_NR&lt;F&gt;&amp; y, mp_rnd_t rnd = GMP_RNDN)<br>
void sub(const FP_NR&lt;F&gt;&amp; x, const FP_NR&lt;F&gt;&amp; y, mp_rnd_t rnd = GMP_RNDN)<br>
void neg(const FP_NR&lt;F&gt;&amp; x)<br>
void mul(const FP_NR&lt;F&gt;&amp; x, const FP_NR&lt;F&gt;&amp; y, mp_rnd_t rnd = GMP_RNDN)<br>
void mul_2si(const FP_NR&lt;F&gt;&amp; x, int c);<br>
void div(const FP_NR&lt;F&gt;&amp; x, const FP_NR&lt;F&gt;&amp; y, mp_rnd_t rnd = GMP_RNDN)<br>
void sqrt(const FP_NR&lt;F&gt;&amp; x, mp_rnd_t rnd = GMP_RNDN)<br>
void pow_si(const FP_NR&lt;F&gt;&amp; x, long c, mp_rnd_t rnd = GMP_RNDN)<br>
void exponential(const FP_NR&lt;F&gt;&amp; x, mp_rnd_t rnd = GMP_RNDN)<br>
void log(const FP_NR&lt;F&gt;&amp; x, mp_rnd_t rnd = GMP_RNDN)<br>
void abs(const FP_NR&lt;F&gt;&amp; x)<br>
void rnd(const FP_NR&lt;F&gt;&amp; x);<br>
void floor(const FP_NR&lt;F&gt;&amp; x);</tt><br>
Sets the value of this object to x + y, x - y, -x, x &times; y, x &times; 2<sup>c</sup>, x / y, square root of x, x<sup>c</sup>, exponential of x, natural logarithm of x, |x|, x rounded to the nearest integer, largest integer not greater than x (respectively).
<tr><td><tt>void addmul(const FP_NR&lt;F&gt;&amp; x, const FP_NR&lt;F&gt;&amp; y, mp_rnd_t rnd = GMP_RNDN)</tt><br>
Adds x &times; y to the current value.
<tr><td><tt>void submul(const FP_NR&lt;F&gt;&amp; x, const FP_NR&lt;F&gt;&amp; y, mp_rnd_t rnd = GMP_RNDN)</tt><br>
Subtracts x &times; y from the current value.
<tr><td><tt>int zero_p() const</tt><br>
Returns non-zero if the current value is zero, 0 otherwise.
<tr><td><tt>void set_nan()</tt><br>
value := NaN.
<tr><td><tt>int is_nan() const</tt><br>
Returns non-zero if the current value is NaN, 0 otherwise.
<tr><td><tt>void swap(FP_NR&lt;F&gt;&amp; x)</tt><br>
Efficiently swaps the values of two FP_NR.
<tr><td><tt>T&amp; getData()</tt><br>
<tt>const T&amp; getData() const</tt><br>
Returns the internal representation of the data.
</table>

Static members:

<table cellpadding="5">
<tr><td><tt>static unsigned int getprec()</tt><br>
Returns the current precision for new FP_NR&lt;F&gt; objects.
<tr><td><tt>static inline unsigned int setprec(unsigned int prec)</tt><br>
Sets the precision of new FP_NR&lt;F&gt; objects. Returns the previous value.
This has no effect is F != mpfr_t.
</table>

Non-member functions:

<table cellpadding="5">
<tr><td><tt>template &lt;class F&gt;<br>ostream&amp; operator&lt;&lt;(ostream&amp; os, const FP_NR&lt;F&gt;&amp; x)</tt><br>
Prints x on stream os.
</table>

Containers:

<tt>typedef FP\_NR&lt;mpfr\_t&gt; Float;</tt><br>
<tt>typedef std::vector&lt;Float&gt; FloatVect;<br>
typedef FP\_mat&lt;mpfr\_t&gt; FloatMatrix;</tt>


<a name="matrix"></a>
#### Matrix&lt;T&gt; ####

Methods:

<table cellpadding="5">
<tr><td><tt>Matrix()</tt><br>
Creates an empty matrix (0 &times; 0).
<tr><td><tt>Matrix(int rows, int cols)</tt><br>
Creates a matrix of dimensions rows &times; cols. All elements are initialized with the default constructor of T.
<tr><td><tt>void clear()</tt><br>
Sets number of rows and the number of columns to 0.
<tr><td><tt>void resize(int rows, int cols)</tt><br>
Sets the dimensions of the matrix, preserving as much as possible of the content. The content of new cells is undefined.
<tr><td><tt>void setRows(int rows)</tt><br>
Sets the number of rows (content is not erased except for deleted rows).
<tr><td><tt>void setCols(int cols)</tt><br>
Sets the number of columns (content is not erased except for deleted columns).
<tr><td><tt>template&lt;class U&gt; void fill(U value)</tt><br>
Fills the matrix with a given value.
<tr><td><tt>void swap(Matrix<T>&amp; m)</tt><br>
Efficiently swaps two matrices.
<tr><td><tt>int getRows() const</tt><br>
Returns the number of rows.
<tr><td><tt>int getCols() const</tt><br>
Returns the number of columns.
<tr><td><tt>T&amp; operator()(int i, int j)</tt><br>
<tt>const T&amp; operator()(int i, int j)</tt><br>
Returns a reference to a coefficient of the matrix.
<tr><td><tt>MatrixRow&lt;T&gt; operator[](int i)</tt><br>
<tt>const MatrixRow&lt;T&gt; operator[](int i) const</tt><br>
Returns a MatrixRow object pointing to the i-th row of the matrix.
</table>

Non-member functions:

<table cellpadding="5">
<tr><td><tt>template&lt;class T&gt; ostream&amp; operator&lt;&lt;(ostream&amp; os, const Matrix&lt;T&gt;&amp; m)</tt><br>
Prints matrix m on stream <code>os</code>.
<tr><td><tt>template&lt;class T&gt; istream&amp; operator&gt;&gt;(istream&amp; is, Matrix&lt;T&gt;&amp; m)</tt><br>
Reads matrix m from stream <code>is</code>.
</table>

Note: a call to <tt>clear</tt>, <tt>resize</tt>, <tt>setRows</tt>,
<tt>setCols</tt> or <tt>swap</tt> invalidates all references returned by
operator() and MatrixRow objects returned by operator[].


<a name="matrix_row"></a>
#### MatrixRow&lt;T&gt; ####

MatrixRow stores a reference to a row of a Matrix. It supports a subset
of operations available on vectors.

Methods:

<table cellpadding="5">
<tr><td><tt>template&lt;class T&gt; ostream&amp; operator&lt;&lt;(ostream&amp; os, const MatrixRow&lt;T&gt;&amp; mr)</tt><br>
Prints mr on stream os.
</table>

<a name="zz_mat"></a>
#### ZZ\_mat ####

Base class: Matrix&lt;Z_NR&lt;ZT&gt;&gt;

Matrix of integers. Same constructors as Matrix.

<a name="fp_mat"></a>
#### FP_mat ####

Base class: Matrix&lt;FP_NR&lt;FT&gt;&gt;

Matrix of floating-point nubmers. Same constructors as Matrix.

<a name="see_also"></a>
### See also ###

[Documentation of std::vector](http://www.sgi.com/tech/stl/Vector.html)

<a name="examples"></a>
## Examples ##

	1. Reduction
	./latticegen r 10 1000 | ./fplll

	2. Fileinput for Reduction
	If the file 'matrix' contains

	[[ 10 11]
	[11 12]]

	Then

	./fplll matrix

	produces

	[[0 1 ]
	[1 0 ]
	]

	3. Random generator
	./latticegen -randseed 1234 r 10 1000 | ./fplll
	./latticegen -randseed time u 10 16 | ./fplll

	4. Solving SVP
	./latticegen r 30 3000 | ./fplll -a svp

<a name="developers"></a>
## Developers ##

**Current developer:**

Martin Albrecht, martinralbrecht@googlemail.com <br>
Damien Stehle, damien.stehle@gmail.com

**Former developers:**

David Cade, david.cade@ens.fr <br>
Xavier Pujol, xavier.pujol@ens-lyon.org

<a name="how_to_cite"></a>
## How to cite ##

	@unpublished{fplll,
	    Note = {Available at \url{http://perso.ens-lyon.fr/damien.stehle}},
	    Title = {{fplll-4.0}, a floating-point {LLL} implementation},
	    Author = {Albrecht, M. and Cad{\'e}, D. and Pujol, X. and Stehl{\'e}, D.}
	}

<a name="acknowledgments"></a>
## Acknowledgments ##

Patrick Pelissier and Paul Zimmermann for dpe.

David H. Bailey for QD.

Martin Albrecht, Sylvain Chevillard, Christoph
Lauter and Gilles Villard for the
"configure/make/make install" packaging.

Martin Albrecht for the incorporation into SAGE.

Timothy Abbott, Michael Abshoff, Martin Albrecht, Bill Allombert,
John Cannon, Sylvain Chevillard, Julien Clement, Andreas Enge,
Jean-Pierre Flori, Laurent Fousse, Guillaume Hanrot, Jens Hermans,
Jerry James, Christoph Lauter, Andrew Novocin, Willem Jan Palenstijn,
Patrick Pelissier, Michael Schneider, Thiemo Seufer, Allan Steel,
Gilles Villard and Paul Zimmermann for their support and for many
suggestions that helped debugging and improving this code.

<a name="new_releases_and_bug_reports"></a>
## New releases and bug reports ##

New releases will be announced at the URL:<br>
[http://perso.ens-lyon.fr/damien.stehle/fplll/](http://perso.ens-lyon.fr/damien.stehle/fplll/)

Bug reports may be sent at:<br>
[damien.stehle@gmail.com](mailto:damien.stehle@gmail.com)

or create an issue.
