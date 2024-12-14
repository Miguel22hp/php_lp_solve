<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html>
	<HEAD>
		<TITLE>Using lpsolve from PHP</TITLE>
		<style TYPE="text/css"> BODY { font-family:verdana,arial,helvetica; margin:15; }
	</style>
	</HEAD>
	<BODY>
					<h1 align="left"><u>Using lpsolve from PHP</u></h1>
<a name="PHP"></a>
<h3>PHP?</h3>
<p>
PHP is a general-purpose scripting language that is especially suited for web development.
PHP generally runs on a web server, taking PHP code as its input and creating web pages as output.
It can also be used for command-line scripting and client-side GUI applications.
PHP can be deployed on most web servers, many operating systems and platforms, and can be used with many relational database management systems.
It is available free of charge, and the PHP Group provides the complete source code for users to build, customize and extend for their own use.
PHP primarily acts as a filter, taking input from a file or stream containing text and/or PHP instructions and outputs another stream of data; most commonly the output will be HTML.
It can automatically detect the language of the user.
From PHP 4, the PHP parser compiles input to produce bytecode for processing by the Zend Engine, giving improved performance over its interpreter predecessor.
Originally designed to create dynamic web pages, PHP's principal focus is server-side scripting, and it is similar to other server-side scripting languages that provide dynamic content from a web server to a client, such as Microsoft's ASP.NET system, Sun Microsystems' JavaServer Pages, and mod_perl.
PHP has also attracted the development of many frameworks that provide building blocks and a design structure to promote rapid application development (RAD).
Some of these include CakePHP, PRADO, Symfony and Zend Framework, offering features similar to other web application frameworks.
</p>
<p>We will not discuss the specifics of PHP here but instead refer the reader to the
<a href="http://www.php.net/">PHP</a> website.<br>
An introduction to PHP can be found at <a href="http://www.w3schools.com/php/default.asp">w3schools</a>
</p>

<a name="PHP_and_lpsolve"></a>
<h3>PHP and lpsolve</h3>

<p>lpsolve is callable from PHP via an extension or module. As such, it looks like lpsolve is fully integrated
with PHP. Matrices can directly be transferred between PHP and lpsolve in both directions. The complete interface
is written in C so it has maximum performance. The whole lpsolve API is implemented with some extra's specific for
PHP (especially for matrix support). So you have full control to the complete lpsolve functionality via the lpsolve
PHP driver.

If you find that this involves too much work to solve an lp model then you can also work via higher-level
script files that can make things a lot easier. See further in this article.
<p>

<a name="Installation"></a>
<h3>Installation</h3>

<p>To make this possible, a driver program is needed: php_phplpsolve55.dll (windows) or phplpsolve55.so (Unix/Linux).<br>
Secondly in the file php.ini, which is a PHP configuration file, the location of the driver must be specified.
The location of this ini file depends on the environment.<br>
Under windows it is commonly \Program Files\php\php.ini<br>
Under Unix/Linux it was found under /etc/php5/cli and /etc/php5/apache2<br>
In that file there is an item extension_dir=. The driver program must be put in the directory specified by that item.<br>
Then an extra entry 'extension' must be added.<br>
Under windows it must be: extension=php_phplpsolve55.dll<br>
Under Unix/Linux it must be: extension=phplpsolve55.so
</p>

<p>Note that there is an alternative way but it is not always working, especially as webservice.
In the php.ini file, specify enable_dl=on<br>
Then in the PHP code, use the following command to load the lpsolve driver: dl('lpsolve.so');<br>
However, this was not tested so use it at own risk.
</p>

<p>To take these changes in effect, the webservice has to be restarted.<br>
Under windows, this is done by restarting the service.<br>
Under Unix/Linux it depends on the system. For example in Ubuntu the command is: sudo /etc/init.d/apache2 restart<br>
This must be done with root privileges: sudo su -
</p>

<p>This driver calls lpsolve via the lpsolve shared library (lpsolve55.dll under Windows
and liblpsolve55.so under Unix/Linux) (archive lp_solve_5.5.0.15_dev.zip/lp_solve_5.5.0.15_dev.tar.gz). This has the advantage that the lpsolve driver doesn't have to
be recompiled when an update of lpsolve is provided. The shared library must be somewhere in the Windows path.</p>

<p>So note the difference between the PHP lpsolve driver that is called (php_)phplpsolve55 and the lpsolve library that implements the
API that is called lpsolve55.</p>

<a name="Testing_the_installation"></a>
<h3>Testing the installation</h3>

<p>Note. In the following text PHP commands are given. They are just provided as is.
However in a real PHP application, all PHP commands must be between &lt;?php and ?&gt;
</p>

<p>Note. Some of these commands return new lines to continue on the next line.
This is fine under CLI (php executed in a command line), but when PHP is used in a web environment and shown in
html, then these newlines are by default just ignored by html.
This gives an output that is not always that readable.
Therefore you can put everything between &lt;pre&gt; &lt;/pre&gt;<br>
This combined with the note from above, put the commands in following block:
</p>

<pre>
&lt;?php
echo "&lt;pre&gt;";
// your php commands
echo "&lt;/pre&gt;";
?&gt;
</pre>

<p>To test if everything is installed correctly, execute the following statement in PHP.</p>

<pre>lpsolve();
</pre>

<p>Note. As stated above, these commands must be between &lt;?php and ?&gt; and when used in html between a pre block as shown below:<p>

<pre>
&lt;?php
echo "&lt;pre&gt;";
lpsolve();
echo "&lt;/pre&gt;";
?&gt;
</pre>

<p>Keep this in mind. This will not be repeated in the following text.</p>

<p>If it gives the following, then everything is ok:</p>

<pre>lpsolve PHP Interface version 5.5.0.6
using lpsolve version 5.5.0.15

Usage: ret = lpsolve("functionname", arg1, arg2, ...)
</pre>

<p>If you get the following:</p>

Windows:

<pre>
PHP Warning:  PHP Startup: Unable to load dynamic library 'F:\php-5.2.6\Release_TS\php_phplpsolve55.dll' - The specified module could not be found. in Unknown on line 0
PHP Fatal error:  Call to undefined function lpsolve() in Command line code on line 1
</pre>

Possible also with a messagebox saying:

<pre>
---------------------------
php.exe - Unable To Locate Component
---------------------------
This application has failed to start because lpsolve55.dll was not found. Re-installing the application may fix this problem.
</pre>

or

Unix/Linux:

<pre>
PHP Warning:  PHP Startup: Unable to load dynamic library '/usr/lib/php5/20060613+lfs/phplpsolve55.so' - liblpsolve55.so: cannot open shared object file: No such file or directory in Unknown on line 0

Fatal error: Call to undefined function lpsolve() in Command line code on line 1
</pre>

<p>
Note that the PHP Warning is not always shown. The Fatal error is.
This was specifically noted in the web environment.
</p>

<p>Then PHP can find the lpsolve driver program, but the driver program cannot find the lpsolve library
that contains the lpsolve implementation.
This library is called lpsolve55.dll under Windows and liblpsolve55.so under Unix/Linux.<br>
Under Windows, the lpsolve55.dll file must be in a directory that in the PATH environment variable.
This path can be shown via the following command in a command prompt: PATH<br>
It is common to place this in the WINDOWS\system32 folder.<br>
<br>
Under Unix/Linux, the liblpsolve55.so shared library must be either in the directories /lib or /usr/lib or in
a directory specified by the LD_LIBRARY_PATH environment variable.
</p>

<p>Note that in a web environment the webserver may need to be restarted after
making changes in the configuration. For example on Ubuntu this is done by the
following command:
</p>

<pre>
sudo /etc/init.d/apache2 restart
</pre>

<p>Another way to check if the lpsolve extension is available in PHP is by
entering the following command:
</p>

<pre>
print_r(get_extension_funcs("lpsolve"));
</pre>

<p>This must return:</p>

<pre>
Array
(
    [0] => lpsolve
)
</pre>

<p>To return the version of lpsolve, the following PHP command can be executed:</p>

<pre>
echo phpversion("lpsolve");
</pre>

<p>This must return:</p>

<pre>5.5.0.6</pre>

<p>Note that this is the version of the PHP driver, not the version of lpsolve itself.</p>

<a name="Solve_an_lp_model_from_PHP_via_lpsolve"></a>
<h3>Solve an lp model from PHP via lpsolve</h3>

<p>To call an lpsolve function, the following syntax must be used:</p>

<pre>ret = lpsolve('functionname', arg1, arg2, ...);</pre>

<p>The return value is optional and depend on the function called. Sometimes it is a single value, sometimes a vector and sometimes a vector of vector. functionname must always be enclosed between single or double
quotes to make it alphanumerical and it is case sensitive. The number and type of arguments depend on the function called.
Some functions even have a variable number of arguments and a different behaviour occurs depending on the type of the argument.
functionname can be (almost) any of the lpsolve API routines (see <a href="lp_solveAPIreference.htm">lp_solve API reference</a>)
plus some extra PHP specific functions.
Most of the lpsolve API routines use or return an lprec structure. To make things more robust in PHP, this structure
is replaced by a handle or the model name. The lprec structures are maintained internally by the lpsolve driver.
The handle is an incrementing number starting from 0.
Starting from driver version 5.5.0.2, it is also possible to use the model name instead of the handle.
This can of course only be done if a name is given to the model. This is done via lpsolve routine
<a href="#set_lp_name">set_lp_name</a> or by specifying the model name in routine <a href="#read_lp">read_lp</a>.
See <a href="#Using_model_name_instead_of_handle">Using model name instead of handle</a>.
</p>

<p>Almost all callable functions can be found in the <a href="lp_solveAPIreference.htm">lp_solve API reference</a>.
Some are exactly as described in the reference guide, others have a slightly different syntax to make maximum
use of the PHP functionality. For example make_lp is used identical as described. But get_variables is slightly
different. In the API reference, this function has two arguments. The first the lp handle and the second the
resulting variables and this array must already be dimensioned. When lpsolve is used from PHP, nothing must
be dimensioned in advance. The lpsolve driver takes care of dimensioning all return variables and they are
always returned as return value of the call to lpsolve. Never as argument to the routine. This can be a single
value as for get_objective or a matrix or vector as in get_variables.
In this case, get_variables returns a 4x1 matrix (vector) with the result of the 4 variables of the lp model.
</p>

<p>Note that you can get a usage of lpsolve, its arguments and the constants that it defines by entering the following in PHP:</p>

<pre>
lpsolve();
$a=get_defined_constants(true); print_r($a[lpsolve]);
</pre>

<p>This will give:</p>

<pre>
lpsolve  PHP Interface version 5.5.0.6
using lpsolve version 5.5.0.15

Usage: ret = lpsolve("functionname", arg1, arg2, ...)
Array
(
    [LE] => 1
    [EQ] => 3
    [GE] => 2
    [FR] => 0
    [SCALE_NONE] => 0
    [SCALE_EXTREME] => 1
    [SCALE_RANGE] => 2
    [SCALE_MEAN] => 3
    [SCALE_GEOMETRIC] => 4
    [SCALE_CURTISREID] => 7
    [SCALE_QUADRATIC] => 8
    [SCALE_LOGARITHMIC] => 16
    [SCALE_USERWEIGHT] => 31
    [SCALE_POWER2] => 32
    [SCALE_EQUILIBRATE] => 64
    [SCALE_INTEGERS] => 128
    [SCALE_DYNUPDATE] => 256
    [SCALE_ROWSONLY] => 512
    [SCALE_COLSONLY] => 1024
    [IMPROVE_NONE] => 0
    [IMPROVE_SOLUTION] => 1
    [IMPROVE_DUALFEAS] => 2
    [IMPROVE_THETAGAP] => 4
    [IMPROVE_BBSIMPLEX] => 8
    [PRICER_FIRSTINDEX] => 0
    [PRICER_DANTZIG] => 1
    [PRICER_DEVEX] => 2
    [PRICER_STEEPESTEDGE] => 3
    [PRICE_PRIMALFALLBACK] => 4
    [PRICE_MULTIPLE] => 8
    [PRICE_PARTIAL] => 16
    [PRICE_ADAPTIVE] => 32
    [PRICE_RANDOMIZE] => 128
    [PRICE_AUTOPARTIAL] => 256
    [PRICE_LOOPLEFT] => 1024
    [PRICE_LOOPALTERNATE] => 2048
    [PRICE_HARRISTWOPASS] => 4096
    [PRICE_TRUENORMINIT] => 16384
    [PRESOLVE_NONE] => 0
    [PRESOLVE_ROWS] => 1
    [PRESOLVE_COLS] => 2
    [PRESOLVE_LINDEP] => 4
    [PRESOLVE_SOS] => 32
    [PRESOLVE_REDUCEMIP] => 64
    [PRESOLVE_KNAPSACK] => 128
    [PRESOLVE_ELIMEQ2] => 256
    [PRESOLVE_IMPLIEDFREE] => 512
    [PRESOLVE_REDUCEGCD] => 1024
    [PRESOLVE_PROBEFIX] => 2048
    [PRESOLVE_PROBEREDUCE] => 4096
    [PRESOLVE_ROWDOMINATE] => 8192
    [PRESOLVE_COLDOMINATE] => 16384
    [PRESOLVE_MERGEROWS] => 32768
    [PRESOLVE_IMPLIEDSLK] => 65536
    [PRESOLVE_COLFIXDUAL] => 131072
    [PRESOLVE_BOUNDS] => 262144
    [PRESOLVE_DUALS] => 524288
    [PRESOLVE_SENSDUALS] => 1048576
    [ANTIDEGEN_NONE] => 0
    [ANTIDEGEN_FIXEDVARS] => 1
    [ANTIDEGEN_COLUMNCHECK] => 2
    [ANTIDEGEN_STALLING] => 4
    [ANTIDEGEN_NUMFAILURE] => 8
    [ANTIDEGEN_LOSTFEAS] => 16
    [ANTIDEGEN_INFEASIBLE] => 32
    [ANTIDEGEN_DYNAMIC] => 64
    [ANTIDEGEN_DURINGBB] => 128
    [ANTIDEGEN_RHSPERTURB] => 256
    [ANTIDEGEN_BOUNDFLIP] => 512
    [CRASH_NONE] => 0
    [CRASH_MOSTFEASIBLE] => 2
    [CRASH_LEASTDEGENERATE] => 3
    [SIMPLEX_PRIMAL_PRIMAL] => 5
    [SIMPLEX_DUAL_PRIMAL] => 6
    [SIMPLEX_PRIMAL_DUAL] => 9
    [SIMPLEX_DUAL_DUAL] => 10
    [NODE_FIRSTSELECT] => 0
    [NODE_GAPSELECT] => 1
    [NODE_RANGESELECT] => 2
    [NODE_FRACTIONSELECT] => 3
    [NODE_PSEUDOCOSTSELECT] => 4
    [NODE_PSEUDONONINTSELECT] => 5
    [NODE_PSEUDORATIOSELECT] => 6
    [NODE_USERSELECT] => 7
    [NODE_WEIGHTREVERSEMODE] => 8
    [NODE_BRANCHREVERSEMODE] => 16
    [NODE_GREEDYMODE] => 32
    [NODE_PSEUDOCOSTMODE] => 64
    [NODE_DEPTHFIRSTMODE] => 128
    [NODE_RANDOMIZEMODE] => 256
    [NODE_GUBMODE] => 512
    [NODE_DYNAMICMODE] => 1024
    [NODE_RESTARTMODE] => 2048
    [NODE_BREADTHFIRSTMODE] => 4096
    [NODE_AUTOORDER] => 8192
    [NODE_RCOSTFIXING] => 16384
    [NODE_STRONGINIT] => 32768
    [NOMEMORY] => -2
    [OPTIMAL] => 0
    [SUBOPTIMAL] => 1
    [INFEASIBLE] => 2
    [UNBOUNDED] => 3
    [DEGENERATE] => 4
    [NUMFAILURE] => 5
    [USERABORT] => 6
    [TIMEOUT] => 7
    [PRESOLVED] => 9
    [PROCFAIL] => 10
    [PROCBREAK] => 11
    [FEASFOUND] => 12
    [NOFEASFOUND] => 13
    [BRANCH_CEILING] => 0
    [BRANCH_FLOOR] => 1
    [BRANCH_AUTOMATIC] => 2
    [BRANCH_DEFAULT] => 3
    [MSG_PRESOLVE] => 1
    [MSG_LPFEASIBLE] => 8
    [MSG_LPOPTIMAL] => 16
    [MSG_MILPEQUAL] => 256
    [MSG_MILPFEASIBLE] => 128
    [MSG_MILPBETTER] => 512
    [NEUTRAL] => 0
    [CRITICAL] => 1
    [SEVERE] => 2
    [IMPORTANT] => 3
    [NORMAL] => 4
    [DETAILED] => 5
    [FULL] => 6
    [Infinite] => 1.0E+30
)
</pre>

<p>The array shows all constants defined by the lpsolve driver and are all
the constants of the lpsolve API. That way one can enter these symbols instead
of their numerical values.
</p>

Also see <a href="#Using_string_constants">Using string constants</a> for an alternative.

<a name="An_example"></a>
<h3>An example</h3>

<p>(Note that you can execute this example by entering command per command as shown below or by executing script example1.php)</p>

<pre>
$lp = lpsolve('make_lp', 0, 4);
lpsolve('set_verbose', $lp, IMPORTANT);
$ret = lpsolve('set_obj_fn', $lp, Array(1, 3, 6.24, 0.1));
$ret = lpsolve('add_constraint', $lp, Array(0, 78.26, 0, 2.9), GE, 92.3);
$ret = lpsolve('add_constraint', $lp, Array(0.24, 0, 11.31, 0), LE, 14.8);
$ret = lpsolve('add_constraint', $lp, Array(12.68, 0, 0.08, 0.9), GE, 4);
$ret = lpsolve('set_lowbo', $lp, 1, 28.6);
$ret = lpsolve('set_lowbo', $lp, 4, 18);
$ret = lpsolve('set_upbo', $lp, 4, 48.98);
$ret = lpsolve('set_col_name', $lp, 1, 'COLONE');
$ret = lpsolve('set_col_name', $lp, 2, 'COLTWO');
$ret = lpsolve('set_col_name', $lp, 3, 'COLTHREE');
$ret = lpsolve('set_col_name', $lp, 4, 'COLFOUR');
$ret = lpsolve('set_row_name', $lp, 1, 'THISROW');
$ret = lpsolve('set_row_name', $lp, 2, 'THATROW');
$ret = lpsolve('set_row_name', $lp, 3, 'LASTROW');
$ret = lpsolve('write_lp', $lp, 'a.lp');
print lpsolve('get_mat', $lp, 1, 2) . "\n";
print lpsolve('solve', $lp) . "\n";
print lpsolve('get_objective', $lp) . "\n";
print_r(lpsolve('get_variables', $lp));
print_r(lpsolve('get_constraints', $lp));
lpsolve('delete_lp', $lp);
</pre>

<p>This gives as output:
</p>

<pre>
78.26
0
31.7827586207
Array
(
    [0] => Array
        (
            [0] => 28.6
            [1] => 0
            [2] => 0
            [3] => 31.8275862069
        )

    [1] => 1
)
Array
(
    [0] => Array
        (
            [0] => 92.3
            [1] => 6.864
            [2] => 391.292827586
        )

    [1] => 1
)
</pre>

<p>Note that get_variables and get_constraints return two results: The result vector and a status. If only the
vector is needed, then variable indexing must be used. For example:</p>

<pre>
$ret = lpsolve('get_variables', $lp);
$x = $ret[0];
$ret = $ret[1];
print_r($x);
print $ret . "\n";
</pre>

<p>Variable x will contain the result vector and ret the return status of the call:</p>

<pre>
Array
(
    [0] => 28.6
    [1] => 0
    [2] => 0
    [3] => 31.8275862069
)
1
</pre>

<p>Don't forget to free the handle and its associated memory when you are done:</p>

<pre>lpsolve('delete_lp', $lp);</pre>

<a name="Using_model_name_instead_of_handle"></a>
<h3>Using model name instead of handle</h3>
From driver version 5.5.0.2, it is possible to use the model name instead of the handle. From the moment the model
has a name, you can use this name instead of the handle. This is best shown by an example. Above example would look
like this:

<pre>
$lp = lpsolve('make_lp', 0, 4);
$ret = lpsolve('set_lp_name', $lp, 'mymodel');
lpsolve('set_verbose', 'mymodel', IMPORTANT);
$ret = lpsolve('set_obj_fn', 'mymodel', Array(1, 3, 6.24, 0.1));
$ret = lpsolve('add_constraint', 'mymodel', Array(0, 78.26, 0, 2.9), GE, 92.3);
$ret = lpsolve('add_constraint', 'mymodel', Array(0.24, 0, 11.31, 0), LE, 14.8);
$ret = lpsolve('add_constraint', 'mymodel', Array(12.68, 0, 0.08, 0.9), GE, 4);
$ret = lpsolve('set_lowbo', 'mymodel', 1, 28.6);
$ret = lpsolve('set_lowbo', 'mymodel', 4, 18);
$ret = lpsolve('set_upbo', 'mymodel', 4, 48.98);
$ret = lpsolve('set_col_name', 'mymodel', 1, 'COLONE');
$ret = lpsolve('set_col_name', 'mymodel', 2, 'COLTWO');
$ret = lpsolve('set_col_name', 'mymodel', 3, 'COLTHREE');
$ret = lpsolve('set_col_name', 'mymodel', 4, 'COLFOUR');
$ret = lpsolve('set_row_name', 'mymodel', 1, 'THISROW');
$ret = lpsolve('set_row_name', 'mymodel', 2, 'THATROW');
$ret = lpsolve('set_row_name', 'mymodel', 3, 'LASTROW');
$ret = lpsolve('write_lp', 'mymodel', 'a.lp');
print lpsolve('get_mat', 'mymodel', 1, 2) . "\n";
print lpsolve('solve', 'mymodel') . "\n";
print lpsolve('get_objective', 'mymodel') . "\n";
print_r(lpsolve('get_variables', 'mymodel'));
print_r(lpsolve('get_constraints', 'mymodel'));
lpsolve('delete_lp', 'mymodel');
</pre>

<p>This gives:</p>

<pre>
78.26
0
31.7827586207
Array
(
    [0] => Array
        (
            [0] => 28.6
            [1] => 0
            [2] => 0
            [3] => 31.8275862069
        )

    [1] => 1
)
Array
(
    [0] => Array
        (
            [0] => 92.3
            [1] => 6.864
            [2] => 391.292827586
        )

    [1] => 1
)
</pre>

<p>So everywhere a handle is needed, you can also use the model name. You can even mix the two methods.
There is also a specific PHP routine to get the handle from the model name: <a href="#get_handle">get_handle</a>.<br>
For example:</p>

<pre>
$lp = lpsolve('get_handle', 'mymodel');
print $lp;
</pre>

<p>This gives:</p>

<pre>
0
</pre>

<p>Don't forget to free the handle and its associated memory when you are done:</p>

<pre>lpsolve('delete_lp', 'mymodel');</pre>

<p>In the next part of this documentation, the handle is used. But if you name the model, the name could thus also be used.</p>

<a name="Matrices"></a>
<h3>Matrices</h3>
lpsolve uses PHP arrays to represent matrices (and vectors).<br>
For example:
<pre>lpsolve('add_constraint', $lp, Array(0.24, 0, 11.31, 0), 1, 14.8);</pre>

<p>Most of the time, variables are used to provide the data:</p>

<pre>lpsolve('add_constraint', $lp, $a1, 1, 14.8);</pre>

<p>Where $a1 is a variable of type array. Sometimes a two-dimensional matrix is used.
In PHP, that is an array of arrays:</p>

<pre>lpsolve('set_mat', $lp, Array(Array(1, 2, 3), Array(4, 5, 6)));</pre>

<p>Array(1, 2, 3) is the first row and Array(4, 5, 6) is the second row.</p>

<p>PHP also supports sparse matrices because of the way arrays are internally supported.<br>
For example:
</p>

<pre>
$a[2] = 3;
$a[5] = 5;

print_r($a);
</pre>

<p>This gives:</p>

<pre>
Array
(
    [2] => 3
    [5] => 5
)
</pre>

<p>The lpsolve driver accepts these as is. No conversion or so is needed. The non-provided
elements are seen as zero-values.
</p>

<p>In fact, the lpsolve driver sees all provided matrices as sparse matrices. lpsolve uses sparse matrices
internally and data can be provided sparse via the ex routines. For example add_constraintex. The lpsolve
driver always uses the ex routines to provide the data to lpsolve. Even if you call from PHP the routine
names that would require a dense matrix (for example add_constraint), the lpsolve driver will always call the
sparse version of the routine (for example add_constraintex). This results in the most performing behaviour.</p>

<p>An important final note. Several lp_solve API routines accept a vector where the first element (element 0) is not used.
Other lp_solve API calls do use the first element. In the PHP interface, there is never an unused element in the matrices.
So if the lp_solve API specifies that the first element is not used, then this element is not in the PHP matrix.</p>

<a name="Maximum_usage_of_matrices_with_lpsolve"></a>
<h3>Maximum usage of matrices with lpsolve</h3>

<p>Because PHP has the array possibility to represent vectors, all lpsolve API routines that need a column or row number to get/set information for that
column/row are extended in the lpsolve PHP driver to also work with vectors. For example set_int in the API can
only set the integer status for one column. If the status for several integer variables must be set, then set_int
must be called multiple times. The lpsolve PHP driver however also allows specifying a vector to set the integer
status of all variables at once. The API call is: $return = lpsolve('set_int', $lp, $column, $must_be_int);. The
matrix version of this call is: $return = lpsolve('set_int', $lp, $must_be_int);.
Here $must_be_int must be an array variable.
The API call to return the integer status of a variable is: $return = lpsolve('is_int', $lp, $column);. The
matrix version of this call is: $is_int = lpsolve('is_int', $lp);<br>
$is_int is again an array variable in this case.
Also note the get_mat and set_mat routines. In PHP these are extended to return/set the complete constraint matrix.
See following example.
</p>

<p>Above example can thus also be done as follows:<br>

(Note that you can execute this example by entering command per command as shown below or by executing script example2.php)
</p>

<pre>
$lp = lpsolve('make_lp', 0, 4);
lpsolve('set_verbose', $lp, IMPORTANT);
$ret = lpsolve('set_obj_fn', $lp, Array(1, 3, 6.24, 0.1));
$ret = lpsolve('add_constraint', $lp, Array(0, 78.26, 0, 2.9), GE, 92.3);
$ret = lpsolve('add_constraint', $lp, Array(0.24, 0, 11.31, 0), LE, 14.8);
$ret = lpsolve('add_constraint', $lp, Array(12.68, 0, 0.08, 0.9), GE, 4);
$ret = lpsolve('set_lowbo', $lp, Array(28.6, 0, 0, 18));
$ret = lpsolve('set_upbo', $lp, Array(Infinite, Infinite, Infinite, 48.98));
$ret = lpsolve('set_col_name', $lp, Array('COLONE', 'COLTWO', 'COLTHREE', 'COLFOUR'));
$ret = lpsolve('set_row_name', $lp, Array('THISROW', 'THATROW', 'LASTROW'));
$ret = lpsolve('write_lp', $lp, 'a.lp');
print_r(lpsolve('get_mat', $lp));
print lpsolve('solve', $lp) . "\n";
print lpsolve('get_objective', $lp) . "\n";
print_r(lpsolve('get_variables', $lp));
print_r(lpsolve('get_constraints', $lp));
lpsolve('delete_lp', $lp);
</pre>

<p>This gives:</p>

<pre>
Array
(
    [0] => Array
        (
            [0] => Array
                (
                    [0] => 0
                    [1] => 78.26
                    [2] => 0
                    [3] => 2.9
                )

            [1] => Array
                (
                    [0] => 0.24
                    [1] => 0
                    [2] => 11.31
                    [3] => 0
                )

            [2] => Array
                (
                    [0] => 12.68
                    [1] => 0
                    [2] => 0.08
                    [3] => 0.9
                )

        )

    [1] => 1
)
0
31.7827586207
Array
(
    [0] => Array
        (
            [0] => 28.6
            [1] => 0
            [2] => 0
            [3] => 31.8275862069
        )

    [1] => 1
)
Array
(
    [0] => Array
        (
            [0] => 92.3
            [1] => 6.864
            [2] => 391.292827586
        )

    [1] => 1
)
</pre>

<p>Note the usage of Infinite in set_upbo. This stands for 'infinity'. Meaning an infinite upper bound.
It is also possible to use -Infinite to express minus infinity. This can for example be used to create a free variable.
Infinite is a constant defined by the lpsolve library.</p>

<p>To show the full power of the matrices, let's now do some matrix calculations to check the solution.
It works further on above example. Note that PHP doesn't support much matrix calculations on arrays.
We only need a matrix multiplication routine to demonstrate the following.
For this the following routine can be used. Include it in the next code to
perform the matrixmultiply:
</p>

<pre>
function matrixmultiply($Array1, $Array2) {
        $rows2 = count($Array2);
        if (is_array($Array2[0])) {
            $dim2 = 2;
            $columns2 = count($Array2[0]);
        }
        else {
            $dim2 = 1;
            $columns2 = 1;
        }

        $rows1 = count($Array1);
        if (is_array($Array1[0])) {
            $dim1 = 2;
            $columns1 = count($Array1[0]);
        }
        else {
            $dim1 = 1;
            if ($rows2 == 1)
                $columns1 = 1;
            else {
                $columns1 = $rows1;
                $rows1 = 1;
            }
        }

        for($i=0; $i&lt;$rows1; $i++){
                for($j=0; $j&lt;$columns2; $j++){
                        $a = 0;
                        for($M=0;$M&lt;$columns1;$M++){
                                if ($dim1 == 2)
                                    $b = $Array1[$i][$M];
                                else if ($rows2 == 1)
                                    $b = $Array1[$i];
                                else
                                    $b = $Array1[$M];
                                $c = $Array2[$M];
                                if ($dim2 == 2)
                                    $c = $c[$j];
                                $a = $a + $b * $c;
                        }
                        if ($dim2 == 2)
                            $ArrayMultipli[$i][$j] = $a;
                        else
                            $ArrayMultipli[$i] = $a;
                }
        }
        return $ArrayMultipli;
}
</pre>

<p>Now do the following calculations:</p>

<pre>
$lp = lpsolve('make_lp', 0, 4);
lpsolve('set_verbose', $lp, IMPORTANT);
$ret = lpsolve('set_obj_fn', $lp, Array(1, 3, 6.24, 0.1));
$ret = lpsolve('add_constraint', $lp, Array(0, 78.26, 0, 2.9), GE, 92.3);
$ret = lpsolve('add_constraint', $lp, Array(0.24, 0, 11.31, 0), LE, 14.8);
$ret = lpsolve('add_constraint', $lp, Array(12.68, 0, 0.08, 0.9), GE, 4);
$ret = lpsolve('set_lowbo', $lp, Array(28.6, 0, 0, 18));
$ret = lpsolve('set_upbo', $lp, Array(Infinite, Infinite, Infinite, 48.98));
$ret = lpsolve('set_col_name', $lp, Array('COLONE', 'COLTWO', 'COLTHREE', 'COLFOUR'));
$ret = lpsolve('set_row_name', $lp, Array('THISROW', 'THATROW', 'LASTROW'));
$ret = lpsolve('write_lp', $lp, 'a.lp');
lpsolve('solve', $lp);

$A = lpsolve('get_mat', $lp);
$A = $A[0];
print_r($A);
$X = lpsolve('get_variables', $lp);
$X = $X[0];
print_r($X);
$B = matrixmultiply($A, $X);
print_r($B);

$C = lpsolve('get_obj_fn', $lp);
$C = $C[0];
print_r($C);
$X = lpsolve('get_variables', $lp);
$X = $X[0];
$obj = matrixmultiply($C, $X);
print_r($obj);
</pre>

<p>So what we have done here is calculate the values of the constraints (RHS) by multiplying the constraint matrix
with the solution vector.<br>
This gives:
</p>

<pre>
Array
(
    [0] => Array
        (
            [0] => 0
            [1] => 78.26
            [2] => 0
            [3] => 2.9
        )

    [1] => Array
        (
            [0] => 0.24
            [1] => 0
            [2] => 11.31
            [3] => 0
        )

    [2] => Array
        (
            [0] => 12.68
            [1] => 0
            [2] => 0.08
            [3] => 0.9
        )

)
Array
(
    [0] => 28.6
    [1] => 0
    [2] => 0
    [3] => 31.8275862069
)
Array
(
    [0] => 92.3
    [1] => 6.864
    [2] => 391.292827586
)
Array
(
    [0] => 1
    [1] => 3
    [2] => 6.24
    [3] => 0.0:
)
Array
(
    [0] => 31.7827586207
)
</pre>

<p>Now take a look at the values of the constraints that lpsolve has found:</p>

<pre>
print_r(lpsolve('get_constraints', $lp));
</pre>

<p>That gives:</p>

<pre>
Array
(
    [0] => Array
        (
            [0] => 92.3
            [1] => 6.864
            [2] => 391.292827586
        )

    [1] => 1
)
</pre>

<p>Exactly the same as the calculated B vector, as expected.</p>

<p>Also the value of the objective is calculated in $obj.
What we have done is calculate the value of the objective by multiplying the objective vector
with the solution vector. Now take a look at the value of the objective that lpsolve has found:</p>

<pre>
print lpsolve('get_objective', $lp);
</pre>

<p>That gives:</p>

<pre>
31.7827586207
</pre>

<p>Again exactly the same as the calculated obj value, as expected.</p>

<a name="Using_string_constants"></a>
<h3>Using string constants</h3>
From driver version 5.5.0.15 on, it is possible to use string constants
everywhere an lp_solve constant is needed or returned. This is best shown by an example.
In the above code we had:

<pre>$lp=lpsolve('make_lp', 0, 4);
lpsolve('set_verbose', $lp, IMPORTANT);
lpsolve('add_constraint', $lp, Array(0, 78.26, 0, 2.9), GE, 92.3);
lpsolve('add_constraint', $lp, Array(0.24, 0, 11.31, 0), LE, 14.8);
lpsolve('add_constraint', $lp, Array(12.68, 0, 0.08, 0.9), GE, 4);</pre>

<p>Note the 3rd parameter on set_verbose and the 4th on add_constraint. These are
    lp_solve constants. One can define all the possible constants in PHP as is
    done in the lpsolve driver and
    then use them in the calls, but that has several disadvantages. First there
    stays the possibility to provide a constant that is not intended for that
    particular call. Another issue is that calls that return a constant are still
    returning it numerical.</p>
<p>Both issues can now be handled by string constants. The above code can be done as
    following with string constants:</p>

<pre>$lp=lpsolve('make_lp', 0, 4);
lpsolve('set_verbose', $lp, &#39;IMPORTANT&#39;);
lpsolve('add_constraint', $lp, Array(0, 78.26, 0, 2.9), &#39;GE&#39;, 92.3);
lpsolve('add_constraint', $lp, Array(0.24, 0, 11.31, 0), &#39;LE&#39;, 14.8);
lpsolve('add_constraint', $lp, Array(12.68, 0, 0.08, 0.9), &#39;GE&#39;, 4);</pre>

<p>This is not only more readable, there is much lesser chance that mistakes are
    being made. The calling routine knows which constants are possible and only
    allows these. So unknown constants or constants that are intended for other
    calls are not accepted. For example:</p>

<pre>lpsolve('set_verbose', $lp, &#39;blabla&#39;);
PHP Fatal error:  lpsolve() [&lt;a href='function.lpsolve'&gt;function.lpsolve&lt;/a&gt;]:
BLABLA: Unknown.

lpsolve('set_verbose', $lp, &#39;GE&#39;);
PHP Fatal error:  lpsolve() [&lt;a href='function.lpsolve'&gt;function.lpsolve&lt;/a&gt;]:
GE: Not allowed here.</pre>

<p>Note the difference between the two error messages. The first says that the
    constant is not known, the second that the constant cannot be used at that
    place.</p>
<p>Constants are case insensitive. Internally they are always translated to upper
    case. Also when returned they will always be in upper case.</p>
<p>The constant names are the ones as specified in the documentation of each API
    routine. There are only 3 exceptions, extensions actually. &#39;LE&#39;, &#39;GE&#39; and &#39;EQ&#39; in
    <a href="add_constraint.htm">add_constraint</a> and <a href="is_constr_type.htm">is_constr_type</a>
    can also be &#39;&lt;&#39;, &#39;&lt;=&#39;, &#39;&gt;&#39;, &#39;&gt;=&#39;, &#39;=&#39;. When returned however, &#39;GE&#39;, &#39;LE&#39;, &#39;EQ&#39;
    will be used.</p>
<p>Also in the matrix version of calls, string constants are possible. For example:</p>

<pre>lpsolve(&#39;set_constr_type&#39;, $lp, Array(&#39;LE&#39;, &#39;EQ&#39;, &#39;GE&#39;));</pre>

<p>Some constants can be a combination of multiple constants. For example
    <a href="set_scaling.htm">set_scaling</a>:</p>

<pre>lpsolve(&#39;set_scaling&#39;, $lp, 3+128);</pre>

<p>With the string version of constants this can be done as following:</p>

<pre>lpsolve(&#39;set_scaling&#39;, $lp, 'SCALE_MEAN|SCALE_INTEGERS');</pre>

<p>| is the OR operator used to combine multiple constants. There may optinally be
    spaces before and after the |.</p>
<p>Not all OR combinations are legal. For example in set_scaling, a choice must be
    made between SCALE_EXTREME, SCALE_RANGE, SCALE_MEAN, SCALE_GEOMETRIC or
    SCALE_CURTISREID. They may not be combined with each other. This is also tested:</p>

<pre>lpsolve(&#39;set_scaling&#39;, $lp, 'SCALE_MEAN|SCALE_RANGE');
PHP Fatal error:  lpsolve() [&lt;a href='function.lpsolve'&gt;function.lpsolve&lt;/a&gt;]:
SCALE_RANGE cannot be combined with SCALE_MEAN</pre>

<p>Everywhere constants must be provided, numeric or string values may be provided.
    The routine automatically interpretes them. </p>
<p>Returning constants is a different
    story. The user must let lp_solve know how to return it. Numerical or as string.
    The default is numerical:</p>

<pre>echo lpsolve(&#39;get_scaling&#39;, $lp);
131</pre>

<p>To let lp_solve return a constant as string, a call to a new function must be
    made: return_constants</p>

<pre>lpsolve(&#39;return_constants&#39;, 1);</pre>

<p>From now on, all returned constants are returned as string:</p>

<pre>echo lpsolve(&#39;get_scaling&#39;, $lp);
SCALE_MEAN|SCALE_INTEGERS</pre>

<p>Also when an array of constants is returned, they are returned as string when
    return_constants is set:</p>

<pre>print_r(lpsolve('get_constr_type', $lp));
Array
(
    [0] => LE
    [1] => EQ
    [2] => GE
)</pre>

<p>This for all routines until return_constants is again called with 0:</p>

<pre>lpsolve(&#39;return_constants&#39;, 0);</pre>

<p>The (new) current setting of return_constants is always returned by the call.
    Even when set:</p>

<pre>echo lpsolve(&#39;return_constants&#39;, 1);
1</pre>

<p>To get the value without setting it, don&#39;t provide the second argument:</p>

<pre>echo lpsolve(&#39;return_constants&#39;);
1</pre>

<p>In the next part of this documentation, return_constants is the default, 0, so all
    constants are returned numerical and provided constants are also numerical. This
    to keep the documentation as compatible as possible with older versions. But
    don&#39;t let you hold that back to use string constants in your code.</p>

<a name="php-files"></a>
<h3>php-files</h3>

<p>PHP can execute a sequence of statements stored in diskfiles. Such files are called
PHP scripts and should have the file type of ".php" as the last part of their filename (extension).</p>

<p>You can put PHP commands in them and execute them at
any time. The PHP script can be executed in the command line via the command php -f script.php.
The script files contain plain ascii data. a PHP file can also be included by other PHP code.
This via the include command. This way it is possible to define routine in one script and execute it in another.
All this can be done via command line or via a webserver.</p>

<p>Note that when used under a webserver like apache that these php files must be in a specific location.
For example /var/www
</p>

<p>The lpsolve PHP distribution contains some example script to demonstrate this.</p>

<h4>example1.php</h4>

<p>Contains the commands as shown in the first example of this article.</p>

<h4>example2.php</h4>

<p>Contains the commands as shown in the second example of this article.</p>

<h4>example3.php</h4>

<p>Contains the commands of a practical example. See further in this article.</p>

<h4>example4.php</h4>

<p>Contains the commands of a practical example. See further in this article.</p>

<h4>example5.php</h4>

<p>Contains the commands of a practical example. See further in this article.</p>

<h4>example6.php</h4>

<p>Contains the commands of a practical example. See further in this article.</p>

<h4>lp_solve.php</h4>

<p>This script uses the API to create a higher-level function called lp_solve.
This function accepts as arguments some matrices and options to create and solve an lp model.
The script must first be inserted in the current code via an include command:</p>

<pre>
include "lp_solve.php";
</pre>

<p>Use lp_solve(); to see its usage:</p>

<pre>LP_SOLVE  Solves mixed integer linear programming problems.

   SYNOPSIS: [obj,x,duals] = lp_solve(f,a,b,e,vlb,vub,xint,scalemode,keep)

      solves the MILP problem

              max v = f'*x
                a*x &lt;&gt; b
                  vlb &lt;= x &lt;= vub
                  x(int) are integer

   ARGUMENTS: The first four arguments are required:

            f: n vector of coefficients for a linear objective function.
            a: m by n matrix representing linear constraints.
            b: m vector of right sides for the inequality constraints.
            e: m vector that determines the sense of the inequalities:
                      e(i) = -1  ==&gt; Less Than
                      e(i) =  0  ==&gt; Equals
                      e(i) =  1  ==&gt; Greater Than
          vlb: n vector of lower bounds. If empty or omitted,
               then the lower bounds are set to zero.
          vub: n vector of upper bounds. May be omitted or empty.
         xint: vector of integer variables. May be omitted or empty.
    scalemode: scale flag. Off when 0 or omitted.
         keep: Flag for keeping the lp problem after it's been solved.
               If omitted, the lp will be deleted when solved.

   OUTPUT: A nonempty output is returned if a solution is found:

          obj: Optimal value of the objective function.
            x: Optimal value of the decision variables.
        duals: solution of the dual problem.
</pre>

<p>Example of usage. To create and solve following lp-model:</p>

<pre>max: -x1 + 2 x2;
C1: 2x1 + x2 &lt; 5;
-4 x1 + 4 x2 &lt;5;

int x2,x1;
</pre>

<p>The following command can be used:</p>

<pre>include "lp_solve.php";
$ret = lp_solve(Array(-1, 2), Array(Array(2, 1), Array(-4, 4)), Array(5, 5), Array(-1, -1), null, null, Array(1, 2));
print_r($ret);
</pre>

This gives:

<pre>
Array
(
    [0] => 3
    [1] => Array
        (
            [0] => 1
            [1] => 2
        )

    [2] => Array
        (
            [0] => 0
            [1] => 0
        )

)
</pre>

<h4>lp_maker.php</h4>

<p>This script is analog to the lp_solve script and also uses the API to create a higher-level function called lp_maker.
This function accepts as arguments some matrices and options to create an lp model. Note that this scripts only
creates a model and returns a handle.
The script must first be inserted in the current code via an include command:</p>

<pre>
include "lp_maker.php";
</pre>

<p>Use lp_maker(); to see its usage:</p>

<pre>LP_MAKER  Makes mixed integer linear programming problems.

   SYNOPSIS: lp_handle = lp_maker(f,a,b,e,vlb,vub,xint,scalemode,setminim)
      make the MILP problem
        max v = f'*x
          a*x &lt;&gt; b
            vlb &lt;= x &lt;= vub
            x(int) are integer

   ARGUMENTS: The first four arguments are required:
            f: n vector of coefficients for a linear objective function.
            a: m by n matrix representing linear constraints.
            b: m vector of right sides for the inequality constraints.
            e: m vector that determines the sense of the inequalities:
                      e(i) &lt; 0  ==&gt; Less Than
                      e(i) = 0  ==&gt; Equals
                      e(i) &gt; 0  ==&gt; Greater Than
          vlb: n vector of non-negative lower bounds. If empty or omitted,
               then the lower bounds are set to zero.
          vub: n vector of upper bounds. May be omitted or empty.
         xint: vector of integer variables. May be omitted or empty.
    scalemode: Autoscale flag. Off when 0 or omitted.
     setminim: Set maximum lp when this flag equals 0 or omitted.

   OUTPUT: lp_handle is an integer handle to the lp created.
</pre>

<p>Example of usage. To create following lp-model:</p>

<pre>max: -x1 + 2 x2;
C1: 2x1 + x2 &lt; 5;
-4 x1 + 4 x2 &lt;5;

int x2,x1;
</pre>

<p>The following command can be used:</p>

<pre>include "lp_maker.php";
$lp = lp_maker(Array(-1, 2), Array(Array(2, 1), Array(-4, 4)), Array(5, 5), Array(-1, -1), null, null, Array(1, 2));
print $lp . "\n";
</pre>

This gives 0.

<p>To solve the model and get the solution:</p>

<pre>lpsolve('solve', $lp);
print lpsolve('get_objective', $lp) . "\n";
$ret = lpsolve('get_variables', $lp);
print_r($ret);
</pre>

This gives:

<pre>3
Array
(
    [0] => Array
        (
            [0] => 1
            [1] => 2
        )

    [1] => 1
)
</pre>

<p>Don't forget to free the handle and its associated memory when you are done:</p>

<pre>lpsolve('delete_lp', $lp);</pre>

<h4>lpdemo.php</h4>

<p>Contains several examples to build and solve lp models.</p>

<h4>ex.php</h4>

<p>Contains several examples to build and solve lp models.
Also solves the lp_examples from the lp_solve distribution.</p>

<a name="A_practical_example"></a>
<h3>A practical example</h3>

<p>We shall illustrate the method of linear programming by means of a simple example,
giving a combination graphical/numerical solution, and then solve both a slightly as well as a substantially
more complicated problem.</p>

<p>Suppose a farmer has 75 acres on which to plant two crops: wheat and barley.
To produce these crops, it costs the farmer (for seed, fertilizer, etc.) $120 per acre for the
wheat and  $210 per acre for the barley. The farmer has $15000 available for expenses.
But after the harvest, the farmer must store the crops while awaiting favourable market conditions.
The farmer has storage space for 4000 bushels. Each acre yields an average of 110 bushels of wheat
or 30 bushels of barley.  If the net profit per bushel of wheat (after all expenses have been subtracted)
is $1.30 and for barley is $2.00, how should the farmer plant the 75 acres to maximize profit?</p>

<p>We begin by formulating the problem mathematically. 
First we express the objective, that is the profit, and the constraints
algebraically, then we graph them, and lastly we arrive at the solution
by graphical inspection and a minor arithmetic calculation.</p>

<p>Let x denote the number of acres allotted to wheat and y the number of acres allotted to barley.
Then the expression to be maximized, that is the profit, is clearly</p>

<p align="center">P = (110)(1.30)x + (30)(2.00)y = 143x + 60y.</p>

<p>There are three constraint inequalities, specified by the limits on expenses, storage and acreage.
They are respectively:</p>

<p align="center">
120x + 210y &lt;= 15000<br>
110x + 30y &lt;= 4000<br>
x + y &lt;= 75
</p>

<p>Strictly speaking there are two more constraint inequalities forced by the fact that the farmer cannot plant
a negative number of acres, namely:</p>

<p align="center">x &gt;= 0, y &gt;= 0.</p>

<p>Next we graph the regions specified by the constraints. The last two say that we only need to consider
the first quadrant in the x-y plane. Here's a graph delineating the triangular region in the first quadrant determined
by the first inequality.</p>

<p><IMG alt="Source" src="O-Matrix1.jpg" border="0"></p>

<p>Now let's put in the other two constraint inequalities.</p>

<p><IMG alt="Source" src="O-Matrix2.jpg" border="0"></p>

<p>The black area is the solution space that holds valid solutions. This means that any point in this area fulfils the
constraints.
</p>

<p>Now let's superimpose on top of this picture a contour plot of the objective function P.</p>

<p><IMG alt="Source" src="O-Matrix3.jpg" border="0"></p>

<p>The lines give a picture of the objective function.
All solutions that intersect with the black area are valid solutions, meaning that this result also fulfils
the set constraints. The more the lines go to the right, the higher the objective value is. The optimal solution
or best objective is a line that is still in the black area, but with an as large as possible value.
</p>

<p>It seems apparent that the maximum value of P will occur on the level curve (that is, level
line) that passes through the vertex of the polygon that lies near (22,53).<br>
It is the intersection of x + y = 75 and 110*x + 30*y = 4000<br>
This is a corner point of the diagram. This is not a coincidence. The simplex algorithm, which is used
by lpsolve, starts from a theorem that the optimal solution is such a corner point.<br>
In fact we can compute the result.
This is done here with an undefined function matrixinverse which is not available in PHP:</p>

<pre>$x = matrixmultiply(inverse(Array(Array(1, 1), Array(110, 30))), array(75, 4000));
print_r($x);
</pre>

With inverse(Array(Array(1, 1), Array(110, 30))) = Array(Array(-0.375, 0.0125), Array(1.375, -0.0125)) that is:

<pre>
$x = matrixmultiply(Array(Array(-0.375, 0.0125), Array(1.375, -0.0125)), array(75, 4000));
print_r($x);
</pre>

That gives:

<pre>
Array
(
    [0] => 21.875
    [1] => 53.125
)
</pre>

<p>The acreage that results in the maximum profit is 21.875 for wheat and 53.125 for barley.
In that case the profit is:</p>

<pre>
$P = matrixmultiply(Array(143, 60), $x);
print_r($P);
</pre>

<p>That gives:</p>

<pre>
Array
(
    [0] => 6315.625
)
</pre>

<p>That is, $6315.625.</p>

<p>Note that these command are in script example3.php</p>

<p>Now, lp_solve comes into the picture to solve this linear programming problem more generally.
After that we will use it to solve two more complicated problems involving more variables
and constraints.</p>

<p>For this example, we use the higher-level script lp_maker to build the model and then some lp_solve API calls
to retrieve the solution. Here is again the usage of lp_maker:</p>

<pre>
 LP_MAKER  Makes mixed integer linear programming problems.

   SYNOPSIS: lp_handle = lp_maker(f,a,b,e,vlb,vub,xint,scalemode,setminim)
      make the MILP problem
        max v = f'*x
          a*x &lt;&gt; b
            vlb &lt;= x &lt;= vub
            x(int) are integer

   ARGUMENTS: The first four arguments are required:
            f: n vector of coefficients for a linear objective function.
            a: m by n matrix representing linear constraints.
            b: m vector of right sides for the inequality constraints.
            e: m vector that determines the sense of the inequalities:
                      e(i) &lt; 0  ==&gt; Less Than
                      e(i) = 0  ==&gt; Equals
                      e(i) &gt; 0  ==&gt; Greater Than
          vlb: n vector of non-negative lower bounds. If empty or omitted,
               then the lower bounds are set to zero.
          vub: n vector of upper bounds. May be omitted or empty.
         xint: vector of integer variables. May be omitted or empty.
    scalemode: Autoscale flag. Off when 0 or omitted.
     setminim: Set maximum lp when this flag equals 0 or omitted.

   OUTPUT: lp_handle is an integer handle to the lp created.
</pre>

<p>Now let's formulate this model with lp_solve:</p>

<pre>
include "lp_maker.php";
$f = Array(143, 60);
$A = Array(Array(120, 210), Array(110, 30), Array(1, 1));
$b = Array(15000, 4000, 75);
$lp = lp_maker($f, $A, $b, Array(-1, -1, -1), null, null, null, 1, 0);
$solvestat = lpsolve('solve', $lp);
$obj = lpsolve('get_objective', $lp);
print $obj . "\n";
$x = lpsolve('get_variables', $lp);
print_r($x);
lpsolve('delete_lp', $lp);
</pre>

That gives:

<pre>
6315.625
Array
(
    [0] => Array
        (
            [0] => 21.875
            [1] => 53.125
        )

    [1] => 1
)
</pre>

<p>Note that these command are in script example4.php</p>

<p>With the higher-level script lp_maker, we provide all data to lp_solve. lp_solve returns a handle (lp) to the
created model. Then the API call 'solve' is used to calculate the optimal solution of the model.
The value of the objective function is retrieved via the API call 'get_objective' and the values of the variables
are retrieved via the API call 'get_variables'. At last, the model is removed from memory via a call to 'delete_lp'.
Don't forget this to free all memory allocated by lp_solve.</p>

<p>The solution is the same answer we obtained before. 
Note that the non-negativity constraints are accounted implicitly because variables are by default non-negative
in lp_solve.</p>

<p>Well, we could have done this problem by hand (as shown in the introduction) because it is very small and it
can be graphically presented.<br>
Now suppose that the farmer is dealing with a third crop, say corn, and that the corresponding data is:</p>

<blockquote>
<table cellSpacing="1" cellPadding="1" border="1">
<tr><td>cost per acre</td><td>$150.75</td></tr>
<tr><td>yield per acre</td><td>125 bushels</td></tr>
<tr><td>profit per bushel</td><td>$1.56</td></tr>
</table>
</blockquote>

<p>With three variables it is already a lot more difficult to show this model graphically. Adding more variables
makes it even impossible because we can't imagine anymore how to represent this. We only have a practical understanding
of 3 dimensions, but beyond that it is all very theoretical.</p>

<p>If we denote the number of acres allotted to corn by z, then the objective function becomes:</p>

<p align="center">P = (110)(1.30)x + (30)(2.00)y + (125)(1.56) = 143x + 60y + 195z</p>

<p>And the constraint inequalities are:</p>

<p align="center">
120x + 210y + 150.75z &lt;= 15000<br>
110x + 30y + 125z &lt;= 4000<br>
x + y + z &lt;= 75<br>
x &gt;= 0, y &gt;= 0, z &gt;= 0
</p>

<p>The problem is solved with lp_solve as follows:</p>

<pre>
include("lp_maker.php");

$f = Array(143, 60, 195);
$A = Array(Array(120, 210, 150.75), Array(110, 30, 125), Array(1, 1, 1));
$b = Array(15000, 4000, 75);
$lp = lp_maker($f, $A, $b, Array(-1, -1, -1), null, null, null, 1, 0);
$solvestat = lpsolve('solve', $lp);
$obj = lpsolve('get_objective', $lp);
print $obj . "\n";
$x = lpsolve('get_variables', $lp);
print_r($x);
lpsolve('delete_lp', $lp);
</pre>

That gives:

<pre>
6986.84210526
Array
(
    [0] => Array
        (
            [0] => 0
            [1] => 56.5789473684
            [2] => 18.4210526316
        )

    [1] => 1
)
</pre>

<p>Note that these command are in script example5.php</p>

<p>So the farmer should ditch the wheat and plant 56.5789 acres of barley and 18.4211 acres of corn.</p>

<p>There is no practical limit on the number of variables and constraints that PHP can handle.
Certainly none that the relatively unsophisticated user will encounter. Indeed, in
many true applications of the technique of linear programming, one needs
to deal with many variables and constraints. The solution of such
a problem by hand is not feasible, and software like PHP is crucial
to success. For example, in the farming problem with which we
have been working, one could have more crops than two or three. Think
agribusiness instead of family farmer. And one could have constraints
that arise from other things beside expenses, storage and acreage limitations. For example:</p>
<ul>
  <li>Availability of seed. This might lead to constraint inequalities like xj &lt; k.</li>
  <li>Personal preferences. Thus the farmer's spouse might have a preference
  for one variety over another and insist on a corresponding planting,
  or something similar with a collection of crops; thus constraint inequalities
  like xi &lt; xj or x1 + x2 &gt; x3.</li>
  <li>Government subsidies. It may take a moment's reflection on the reader's part,
  but this could lead to inequalities like xj &gt; k.</li>
</ul>

<p>Below is a sequence of commands that solves exactly such a problem.
You should be able to recognize the objective expression and the constraints from the data that is entered. 
But as an aid, you might answer the following questions:
</p>

<ul>
  <li>How many crops are under consideration?</li>
  <li>What are the corresponding expenses? How much is available for expenses?</li>
  <li>What are the yields in each case? What is the storage capacity?</li>
  <li>How many acres are available?</li>
  <li>What crops are constrained by seed limitations? To what extent?</li>
  <li>What about preferences?</li>
  <li>What are the minimum acreages for each crop?</li>
</ul>

<pre>
include("lp_maker.php");

$f = Array(110*1.3, 30*2.0, 125*1.56, 75*1.8, 95*.95, 100*2.25, 50*1.35);
$A = Array(Array(120, 210, 150.75, 115, 186, 140, 85), Array(110, 30, 125, 75, 95, 100, 50), Array(1, 1, 1, 1, 1, 1, 1), Array(1, -1, 0, 0, 0, 0, 0), Array(0, 0, 1, 0, -2, 0, 0), Array(0, 0, 0, -1, 0, -1, 1));
$b = Array(55000, 40000, 400, 0, 0, 0);
$lp = lp_maker($f, $A, $b, Array(-1, -1, -1, -1, -1, -1), Array(10, 10, 10, 10, 20, 20, 20), Array(100, Infinite, 50, Infinite, Infinite, 250, Infinite), null, 1, 0);
$solvestat = lpsolve('solve', $lp);
$obj = lpsolve('get_objective', $lp);
print $obj . "\n";
$x = lpsolve('get_variables', $lp);
print_r($x);
lpsolve('delete_lp', $lp);
</pre>

That gives:

<pre>
75398.0434783
Array
(
    [0] => Array
        (
            [0] => 10
            [1] => 10
            [2] => 40
            [3] => 45.652173913
            [4] => 20
            [5] => 250
            [6] => 20
        )

    [1] => 1
)
</pre>

<p>Note that these command are in script example6.php</p>

<p>Note that we have used in this formulation the vlb and vub arguments of lp_maker. This to set lower and upper bounds
on variables. This could have been done via extra constraints, but it is more performant to set bounds on variables.
Also note that Infinity is used for variables that have no upper limit.
</p>

<p>Note that despite the complexity of the problem, lp_solve solves it almost instantaneously. It seems the
farmer should bet the farm on crop number 6. We strongly suggest
you alter the expense and/or the storage limit in the problem and see
what effect that has on the answer.</p>

<a name="Another,_more_theoretical,_example"></a>
<h3>Another, more theoretical, example</h3>

<p>Suppose we want to solve the following linear program using PHP:</p>
<p align="center">
max 4x1 + 2x2 + x3<br>
s. t. 2x1 + x2 &lt;= 1<br>
x1 + 2x3 &lt;= 2<br>
x1 + x2 + x3 = 1<br>
x1 &gt;= 0<br>
x1 &lt;= 1<br>
x2 &gt;= 0<br>
x2 &lt;= 1<br>
x3 &gt;= 0<br>
x3 &lt;= 2<br>
</p>

<p>Convert the LP into PHP format we get:</p>

<p align="center">
$f = Array(4, 2, 1);<br>
$A = Array(Array(2, 1, 0), Array(1, 0, 2), Array(1, 1, 1));<br>
$b = Array(1, 2, 1);
</p>

<p>Note that constraints on single variables are not put in the constraint matrix.
lp_solve can set bounds on individual variables and this is more performant than creating
additional constraints. These bounds are:
</p>

<p align="center">
$l = Array( 0, 0, 0);<br>
$u = Array( 1, 1, 2);
</p>

<p>Now lets enter this in PHP:</p>

<pre>
$f = Array(4, 2, 1);
$A = Array(Array(2, 1, 0), Array(1, 0, 2), Array(1, 1, 1));
$b = Array(1, 2, 1);
$l = Array( 0, 0, 0);
$u = Array( 1, 1, 2);
</pre>

<p>Now solve the linear program using PHP: Use the commands</p>

<pre>
include "lp_maker.php";
$lp = lp_maker($f, $A, $b, Array(-1, -1, -1), $l, $u, null, 1, 0);
$solvestat = lpsolve('solve', $lp);
$obj = lpsolve('get_objective', $lp);
print $obj . "\n";
$x = lpsolve('get_variables', $lp);
print_r($x);
lpsolve('delete_lp', $lp);
</pre>

This gives:

<pre>
2.5
Array
(
    [0] => Array
        (
            [0] => 0.5
            [1] => 0
            [2] => 0.5
        )

    [1] => 1
)
</pre>

<p>What to do when some of the variables are missing ?<br>
For example, suppose there are no lower bounds on the variables. In this case define l to be the empty set using the PHP command:
</p>

<pre>
l = null
</pre>

<p>This has the same effect as before, because lp_solve has as default lower bound for variables 0.</p>

<p>But what if you want that variables may also become negative?<br>
Then you can use -Infinite as lower bounds:</p>

<pre>
$l = Array(-Infinite, -Infinite, -Infinite);
</pre>

<p>Solve this and you get a different result:</p>

<pre>
include "lp_maker.php";
$lp = lp_maker($f, $A, $b, Array(-1, -1, -1), $l, $u, null, 1, 0);
$solvestat = lpsolve('solve', $lp);
$obj = lpsolve('get_objective', $lp);
print $obj . "\n";
$x = lpsolve('get_variables', $lp);
print_r($x);
lpsolve('delete_lp', $lp);
</pre>

This gives:

<pre>
2.66666666667
Array
(
    [0] => Array
        (
            [0] => 0.666666666667
            [1] => -0.333333333333
            [2] => 0.666666666667
        )

    [1] => 1
)
</pre>

<a name="Overview_of_API_routines"></a>
<h3>Overview of API routines</h3>

<!--
<p>Note again that the PHP command 'help lpsolve' gives an overview of all functions that can be called via lpsolve with their arguments and return values.</p>
-->

<p>Note that everwhere where lp is used as argument that this can be a handle (lp) or the models name.</p>

<ul>
	<li>
		<a href="add_column.htm">add_column, add_columnex</a>
		<ul>
			<li>return = lpsolve('add_column', lp,
    [column])

			<li>return = lpsolve('add_columnex', lp,
    [column])

			<li>Both have the same interface from <a href="add_column.htm">add_column</a> but act as <a href="add_column.htm">add_columnex</a></li>
		</ul>
    <li>
        <a href="add_constraint.htm">add_constraint, add_constraintex</a>
        <ul>
            <li>return = lpsolve('add_constraint', lp,
    [row], constr_type, rh)
            <li>return = lpsolve('add_constraintex', lp,
    [row], constr_type, rh)
            <li>Both have the same interface from <a href="add_constraint.htm">add_constraint</a> but act as <a href="add_constraint.htm">add_constraintex</a></li>
        </ul>
    <li>
        <a href="add_SOS.htm">add_SOS</a>
        <ul>
            <li>return = lpsolve('add_SOS', lp, name,
    sostype, priority, [sosvars], [weights])
            <li>The <i>count</i> argument in the API documentation is not needed in PHP since the number of elements is derived from the size of the sosvars and weights matrices. These must have the same size.</li>
        </ul>
    <li>
        <a href="column_in_lp.htm">column_in_lp</a>
        <ul>
            <li>return = lpsolve('column_in_lp', lp,
    [column])
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="copy_lp.htm">copy_lp</a>
        <ul>
            <li>lp_handle = lpsolve('copy_lp', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="default_basis.htm">default_basis</a>
        <ul>
            <li>lpsolve('default_basis', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="del_column.htm">del_column</a>
        <ul>
            <li>return = lpsolve('del_column', lp, column)

            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="del_constraint.htm">del_constraint</a>
        <ul>
            <li>return = lpsolve('del_constraint', lp,
    del_row)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="delete_lp.htm">delete_lp</a>
        <ul>
            <li>lpsolve('delete_lp', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="free_lp.htm">free_lp</a>
        <ul>
            <li>lpsolve('free_lp', lp)
            <li>lp is not changed as in the lpsolve API since it is a read_only input parameter. So it acts the same as delete_lp.</li>
        </ul>
    <li>
        <a href="get_anti_degen.htm">get_anti_degen</a>
        <ul>
            <li>return = lpsolve('get_anti_degen', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_basis.htm">get_basis</a>
        <ul>
            <li>[bascolumn] = lpsolve('get_basis', lp {,
    nonbasic})
            <li>The <i>bascolumn</i> argument in the API documentation is here the return value. The <i>nonbasic</i> argument is optional in PHP. If not provided, then 0 is used.</li>
        </ul>
    <li>
        <a href="get_basiscrash.htm">get_basiscrash</a>
        <ul>
            <li>return = lpsolve('get_basiscrash', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_bb_depthlimit.htm">get_bb_depthlimit</a>
        <ul>
            <li>return = lpsolve('get_bb_depthlimit', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_bb_floorfirst.htm">get_bb_floorfirst</a>
        <ul>
            <li>return = lpsolve('get_bb_floorfirst', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_bb_rule.htm">get_bb_rule</a>
        <ul>
            <li>return = lpsolve('get_bb_rule', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_bounds_tighter.htm">get_bounds_tighter</a>
        <ul>
            <li>return = lpsolve('get_bounds_tighter', lp)

            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_break_at_value.htm">get_break_at_value</a>
        <ul>
            <li>return = lpsolve('get_break_at_value', lp)

            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_col_name.htm">get_col_name</a>
        <ul>
            <li>name = lpsolve('get_col_name', lp, column)

            <li>[names] = lpsolve('get_col_name', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_column.htm">get_column</a>
        <a href="get_column.htm">get_columnex</a>
        <ul>
            <li>[column, return] = lpsolve('get_column', lp, col_nr)
            <li>[column, return] = lpsolve('get_columnex', lp, col_nr)
            <li>The <i>column</i> argument in
    the API documentation is here the first return value.
            <li>The return code of the call is the second return value.</li>
        </ul>
    <li>
        <a href="get_constr_type.htm">get_constr_type</a>
        <ul>
            <li>return = lpsolve('get_constr_type', lp,
    row)
            <li>[constr_type] = lpsolve('get_constr_type',
    lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_constr_value.htm">get_constr_value</a>
        <ul>
            <li>return = lpsolve('get_constr_value', lp, row {, primsolution})
            <li>The primsolution argument is optional. If not provided, then the solution of last solve is used.</li>
        </ul>
    <li>
        <a href="get_constraints.htm">get_constraints</a>
        <ul>
            <li>[constr, return] = lpsolve('get_constraints',
    lp)
            <li>The <i>constr</i> argument in
    the API documentation is here the first return value.
            <li>The return code of the call is the second return value.</li>
        </ul>
    <li>
        <a href="get_sensitivity_rhs.htm">get_dual_solution</a>
        <ul>
            <li>[duals, return] = lpsolve('get_dual_solution',
    lp)
            <li>The <i>duals</i> argument in
    the API documentation is here the first return value.
            <li>In the API, element 0 is not used and values start
    from element 1. In PHP, there is no unused element in the matrix.
            <li>The return code of the call is the second return value.</li>
        </ul>
    <li>
        <a href="get_epsb.htm">get_epsb</a>
        <ul>
            <li>return = lpsolve('get_epsb', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_epsd.htm">get_epsd</a>
        <ul>
            <li>return = lpsolve('get_epsd', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_epsel.htm">get_epsel</a>
        <ul>
            <li>return = lpsolve('get_epsel', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_epsint.htm">get_epsint</a>
        <ul>
            <li>return = lpsolve('get_epsint', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_epsperturb.htm">get_epsperturb</a>
        <ul>
            <li>return = lpsolve('get_epsperturb', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_epspivot.htm">get_epspivot</a>
        <ul>
            <li>return = lpsolve('get_epspivot', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_improve.htm">get_improve</a>
        <ul>
            <li>return = lpsolve('get_improve', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_infinite.htm">get_infinite</a>
        <ul>
            <li>return = lpsolve('get_infinite', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_lowbo.htm">get_lowbo</a>
        <ul>
            <li>return = lpsolve('get_lowbo', lp, column)
            <li>[return] = lpsolve('get_lowbo', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_lp_index.htm">get_lp_index</a>
        <ul>
            <li>return = lpsolve('get_lp_index', lp,
    orig_index)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_lp_name.htm">get_lp_name</a>
        <ul>
            <li>name = lpsolve('get_lp_name', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_mat.htm">get_mat</a>
        <ul>
            <li>value = lpsolve('get_mat', lp, row, col)
            <li>[matrix, return] = lpsolve('get_mat', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix in the first return value.
                The return code of the call is the second return value.</li>
        </ul>
    <li>
        <a href="get_max_level.htm">get_max_level</a>
        <ul>
            <li>return = lpsolve('get_max_level', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_maxpivot.htm">get_maxpivot</a>
        <ul>
            <li>return = lpsolve('get_maxpivot', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_mip_gap.htm">get_mip_gap</a>
        <ul>
            <li>return = lpsolve('get_mip_gap', lp,
    absolute)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_nameindex.htm">get_nameindex</a>
        <ul>
            <li>return = lpsolve('get_nameindex', lp, name, isrow)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_Ncolumns.htm">get_Ncolumns</a>
        <ul>
            <li>return = lpsolve('get_Ncolumns', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_negrange.htm">get_negrange</a>
        <ul>
            <li>return = lpsolve('get_negrange', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_nonzeros.htm">get_nonzeros</a>
        <ul>
            <li>return = lpsolve('get_nonzeros', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_Norig_columns.htm">get_Norig_columns</a>
        <ul>
            <li>return = lpsolve('get_Norig_columns', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_Norig_rows.htm">get_Norig_rows</a>
        <ul>
            <li>return = lpsolve('get_Norig_rows', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_Nrows.htm">get_Nrows</a>
        <ul>
            <li>return = lpsolve('get_Nrows', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_obj_bound.htm">get_obj_bound</a>
        <ul>
            <li>return = lpsolve('get_obj_bound', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_objective.htm">get_objective</a>
        <ul>
            <li>return = lpsolve('get_objective', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_orig_index.htm">get_orig_index</a>
        <ul>
            <li>return = lpsolve('get_orig_index', lp,
    lp_index)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_col_name.htm">get_origcol_name</a>
        <ul>
            <li>name = lpsolve('get_origcol_name', lp,
    column)
            <li>[names] = lpsolve('get_origcol_name', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_row_name.htm">get_origrow_name</a>
        <ul>
            <li>name = lpsolve('get_origrow_name', lp,
    row)
            <li>[names] = lpsolve('get_origrow_name', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_pivoting.htm">get_pivoting</a>
        <ul>
            <li>return = lpsolve('get_pivoting', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_presolve.htm">get_presolve</a>
        <ul>
            <li>return = lpsolve('get_presolve', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_presolveloops.htm">get_presolveloops</a>
        <ul>
            <li>return = lpsolve('get_presolveloops', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_primal_solution.htm">get_primal_solution</a>
        <ul>
            <li>[pv, return] = lpsolve('get_primal_solution',
    lp)
            <li>The <i>pv</i> argument in the
    API documentation is here the first return value.
            <li>The return code of the call is the second return value.</li>
        </ul>
    <li>
        <a href="get_print_sol.htm">get_print_sol</a>
        <ul>
            <li>return = lpsolve('get_print_sol', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_constraints.htm">get_ptr_constraints</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="get_sensitivity_rhs.htm">get_ptr_dualsolution</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="get_primal_solution.htm">get_ptr_primal_solution</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="get_sensitivity_obj.htm">get_ptr_sensitivity_obj, get_ptr_sensitivity_objex</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="get_sensitivity_rhs.htm">get_ptr_sensitivity_rhs</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="get_variables.htm">get_ptr_variables</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="get_rh.htm">get_rh</a>
        <ul>
            <li>return = lpsolve('get_rh', lp, row)
            <li>[rh] = lpsolve('get_rh', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_rh_range.htm">get_rh_range</a>
        <ul>
            <li>return = lpsolve('get_rh_range', lp, row)
            <li>[rh_ranges] = lpsolve('get_rh_range', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_row.htm">get_row</a>
        <a href="get_row.htm">get_rowex</a>
        <ul>
            <li>[row, return] = lpsolve('get_row', lp, row_nr)
            <li>[row, return] = lpsolve('get_rowex', lp, row_nr)
            <li>The <i>row</i> argument in the
    API documentation is here the first return value.
            <li>In the API, element 0 is not used and values start
    from element 1. In PHP, there is no unused element in the matrix.
            <li>The return code of the call is the second return value.</li>
        </ul>
    <li>
        <a href="get_row_name.htm">get_row_name</a>
        <ul>
            <li>name = lpsolve('get_row_name', lp, row)
            <li>[names] = lpsolve('get_row_name', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_scalelimit.htm">get_scalelimit</a>
        <ul>
            <li>return = lpsolve('get_scalelimit', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_scaling.htm">get_scaling</a>
        <ul>
            <li>return = lpsolve('get_scaling', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_sensitivity_obj.htm">get_sensitivity_obj, get_sensitivity_objex</a>
        <ul>
            <li>[objfrom, objtill, objfromvalue, objtillvalue,
    return] = lpsolve('get_sensitivity_obj', lp)
            <li>[objfrom, objtill, objfromvalue, objtillvalue,
    return] = lpsolve('get_sensitivity_objex', lp)
            <li>The <i>objfrom</i>, <i>objtill</i>, <i>objfromvalue</i>, <i>objtillvalue</i> arguments in the API documentation
    are here the return values. Note that PHP allows the return of fewer
    variables. For example if only objfrom and objtill are needed then the
    call can be [objfrom, objtill] = lpsolve('get_sensitivity_obj',
    lp). The unrequested values are even not calculated.
            <li>Since the API routine doesn't calculate the <i>objtillvalue</i> value at this time, PHP always
    returns a zero vector for this.
            <li>The return code of the call is the last value.
            <li>get_sensitivity_obj and get_sensitivity_objex are both implemented, but have the same functionality.</li>
        </ul>
    <li>
        <a href="get_sensitivity_rhs.htm">get_sensitivity_rhs, get_sensitivity_rhsex</a>
        <ul>
            <li>[duals, dualsfrom, dualstill, return] =
    lpsolve('get_sensitivity_rhs', lp)
            <li>[duals, dualsfrom, dualstill, return] =
    lpsolve('get_sensitivity_rhsex', lp)
            <li>The <i>duals</i>, <i>dualsfrom</i>, <i>dualstill</i>
    arguments in the API documentation are here the return values. Note that
    PHP allows the return of fewer variables. For example if only duals is
    needed then the call can be [duals] = lpsolve('get_sensitivity_rhs',
    lp). The unrequested values are even not calculated.
            <li>The return code of the call is the last value.
            <li>get_sensitivity_rhs and get_sensitivity_rhsex are both implemented, but have the same functionality.</li>
        </ul>
    <li>
        <a href="get_simplextype.htm">get_simplextype</a>
        <ul>
            <li>return = lpsolve('get_simplextype', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_solutioncount.htm">get_solutioncount</a>
        <ul>
            <li>return = lpsolve('get_solutioncount', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_solutionlimit.htm">get_solutionlimit</a>
        <ul>
            <li>return = lpsolve('get_solutionlimit', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_status.htm">get_status</a>
        <ul>
            <li>return = lpsolve('get_status', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_statustext.htm">get_statustext</a>
        <ul>
            <li>return = lpsolve('get_statustext', lp,
    statuscode)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_timeout.htm">get_timeout</a>
        <ul>
            <li>return = lpsolve('get_timeout', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_total_iter.htm">get_total_iter</a>
        <ul>
            <li>return = lpsolve('get_total_iter', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_total_nodes.htm">get_total_nodes</a>
        <ul>
            <li>return = lpsolve('get_total_nodes', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_upbo.htm">get_upbo</a>
        <ul>
            <li>return = lpsolve('get_upbo', lp, column)
            <li>[upbo] = lpsolve('get_upbo', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_var_branch.htm">get_var_branch</a>
        <ul>
            <li>return = lpsolve('get_var_branch', lp,
    column)
            <li>[var_branch] = lpsolve('get_var_branch',
    lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_sensitivity_rhs.htm">get_var_dualresult</a>
        <ul>
            <li>return = lpsolve('get_var_dualresult', lp,
    index)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_primal_solution.htm">get_var_primalresult</a>
        <ul>
            <li>return = lpsolve('get_var_primalresult',
    lp, index)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_var_priority.htm">get_var_priority</a>
        <ul>
            <li>return = lpsolve('get_var_priority', lp,
    column)
            <li>[var_priority] = lpsolve('get_var_priority',
    lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="get_variables.htm">get_variables</a>
        <ul>
            <li>[var, return] = lpsolve('get_variables',
    lp)
            <li>The <i>var</i> argument in the
    API documentation is here the first return value.
            <li>The return code of the call is the second return value.</li>
        </ul>
    <li>
        <a href="get_verbose.htm">get_verbose</a>
        <ul>
            <li>return = lpsolve('get_verbose', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="get_working_objective.htm">get_working_objective</a>
        <ul>
            <li>return = lpsolve('get_working_objective',
    lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="guess_basis.htm">guess_basis</a>
        <ul>
            <li>[basisvector, return] = lpsolve('guess_basis', lp, [guessvector])
            <li>In the API, element 0 of <i>guessvector</i> is not used and values start from element 1. In PHP, there is no unused element in the matrix.</li>
            <li>In the API, element 0 of <i>basisvector</i> is not used and values start from element 1. In PHP, there is no unused element in the matrix.</li>
        </ul>
    <li>
        <a href="has_BFP.htm">has_BFP</a>
        <ul>
            <li>return = lpsolve('has_BFP', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="has_XLI.htm">has_XLI</a>
        <ul>
            <li>return = lpsolve('has_XLI', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_add_rowmode.htm">is_add_rowmode</a>
        <ul>
            <li>return = lpsolve('is_add_rowmode', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_anti_degen.htm">is_anti_degen</a>
        <ul>
            <li>return = lpsolve('is_anti_degen', lp,
    testmask)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_binary.htm">is_binary</a>
        <ul>
            <li>return = lpsolve('is_binary', lp, column)
            <li>[binary] = lpsolve('is_binary', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="is_break_at_first.htm">is_break_at_first</a>
        <ul>
            <li>return = lpsolve('is_break_at_first', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_constr_type.htm">is_constr_type</a>
        <ul>
            <li>return = lpsolve('is_constr_type', lp,
    row, mask)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_debug.htm">is_debug</a>
        <ul>
            <li>return = lpsolve('is_debug', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_feasible.htm">is_feasible</a>
        <ul>
            <li>return = lpsolve('is_feasible', lp,
    [values] {, threshold})
            <li>The threshold argument is optional.
                When not provided, the value of <A href="get_epsint.htm">get_epsint</A> will be taken.</li>
        </ul>
    <li>
        <a href="is_unbounded.htm">is_free</a>
        <a href="is_unbounded.htm">is_unbounded</a>
        <ul>
            <li>return = lpsolve('is_free', lp, column)
            <li>return = lpsolve('is_unbounded', lp, column)
            <li>[free] = lpsolve('is_free', lp)
            <li>[free] = lpsolve('is_unbounded', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="is_infinite.htm">is_infinite</a>
        <ul>
            <li>return = lpsolve('is_infinite', lp, value)

            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_int.htm">is_int</a>
        <ul>
            <li>return = lpsolve('is_int', lp, column)
            <li>[int] = lpsolve('is_int', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="is_integerscaling.htm">is_integerscaling</a>
        <ul>
            <li>return = lpsolve('is_integerscaling', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_maxim.htm">is_maxim</a>
        <ul>
            <li>return = lpsolve('is_maxim', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_nativeBFP.htm">is_nativeBFP</a>
        <ul>
            <li>return = lpsolve('is_nativeBFP', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_nativeXLI.htm">is_nativeXLI</a>
        <ul>
            <li>return = lpsolve('is_nativeXLI', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_negative.htm">is_negative</a>
        <ul>
            <li>return = lpsolve('is_negative', lp,
    column)
            <li>[negative] = lpsolve('is_negative', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="is_piv_mode.htm">is_piv_mode</a>
        <ul>
            <li>return = lpsolve('is_piv_mode', lp,
    testmask)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_piv_rule.htm">is_piv_rule</a>
        <ul>
            <li>return = lpsolve('is_piv_rule', lp, rule)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_presolve.htm">is_presolve</a>
        <ul>
            <li>return = lpsolve('is_presolve', lp,
    testmask)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_scalemode.htm">is_scalemode</a>
        <ul>
            <li>return = lpsolve('is_scalemode', lp,
    testmask)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_scaletype.htm">is_scaletype</a>
        <ul>
            <li>return = lpsolve('is_scaletype', lp,
    scaletype)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_semicont.htm">is_semicont</a>
        <ul>
            <li>return = lpsolve('is_semicont', lp,
    column)
            <li>[semicont] = lpsolve('is_semicont', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="is_SOS_var.htm">is_SOS_var</a>
        <ul>
            <li>return = lpsolve('is_SOS_var', lp, column)

            <li>[SOS_var] = lpsolve('is_SOS_var', lp)
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows retrieving the values into a PHP matrix.</li>
        </ul>
    <li>
        <a href="is_trace.htm">is_trace</a>
        <ul>
            <li>return = lpsolve('is_trace', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="is_use_names.htm">is_use_names</a>
        <ul>
            <li>return = lpsolve('is_use_names', lp, isrow)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="lp_solve_version.htm">lp_solve_version</a>
        <ul>
            <li>versionstring = lpsolve('lp_solve_version')
            <li>The lpsolve API routine returns the version information in 4 provided argument variables while the PHP version returns the information as a string in the format major.minor.release.build</li>
        </ul>
    <li>
        <a href="make_lp.htm">make_lp</a>
        <ul>
            <li>lp_handle = lpsolve('make_lp', rows, columns)
            <li>lp_handle is not a pointer to an lprec structure as in the API, but an incrementing handle number starting from 0.</li>
        </ul>
    <li>
        <a href="print_constraints.htm">print_constraints</a>
        <ul>
			<li>lpsolve('print_constraints', lp {,
    columns})

			<li>columns is optional. If not specified, then 1 is
    used.

			<li>First call set_outputfile to specify where the
    information is written to. In the API documentation it is written that by
    default, the output goes to stdout, but under PHP (Windows) this means
    that the output is not shown.
            <li>The same information can also be obtained via lpsolve('get_constraints', lp). This shows the result on screen.</li>
        </ul>
    <li>
        <a href="print_debugdump.htm">print_debugdump</a>
        <ul>
            <li>return = lpsolve('print_debugdump', lp,
    filename)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="print_duals.htm">print_duals</a>
        <ul>
			<li>lpsolve('print_duals', lp)

			<li>First call set_outputfile to specify where the
    information is written to. In the API documentation it is written that by
    default, the output goes to stdout, but under PHP (Windows) this means
    that the output is not shown.
            <li>The same information can be obtained via lpsolve('get_dual_solution', lp). This shows the result on screen.</li>
        </ul>
    <li>
        <a href="print_lp.htm">print_lp</a>
        <ul>
			<li>lpsolve('print_lp', lp)

			<li>First call set_outputfile to specify where the information is written to.
			    In the API documentation it is written that by default, the output goes to stdout, but under PHP (Windows) this means that the output is not shown.</li>
        </ul>
    <li>
        <a href="print_objective.htm">print_objective</a>
        <ul>
			<li>lpsolve('print_objective', lp)

			<li>First call set_outputfile to specify where the
    information is written to. In the API documentation it is written that by
    default, the output goes to stdout, but under PHP (Windows) this means
    that the output is not shown.
            <li>The same information can be obtained via lpsolve('get_objective', lp). This shows the result on screen.</li>
        </ul>
    <li>
        <a href="print_scales.htm">print_scales</a>
        <ul>
			<li>lpsolve('print_scales', lp)

			<li>First call set_outputfile to specify where the information is written to.
			    In the API documentation it is written that by default, the output goes to stdout, but under PHP (Windows) this means that the output is not shown.</li>
        </ul>
    <li>
        <a href="print_solution.htm">print_solution</a>
        <ul>
			<li>lpsolve('print_solution', lp {, columns})

			<li>columns is optional. If not specified, then 1 is
    used.

			<li>First call set_outputfile to specify where the
    information is written to. In the API documentation it is written that by
    default, the output goes to stdout, but under PHP (Windows) this means
    that the output is not shown.
            <li>The same information can also be obtained via lpsolve('get_variables', lp). This shows the result on screen.</li>
        </ul>
    <li>
        <a href="print_str.htm">print_str</a>
        <ul>
			<li>lpsolve('print_str', lp, str)

			<li>First call set_outputfile to specify where the information is written to.
			    In the API documentation it is written that by default, the output goes to stdout, but under PHP (Windows) this means that the output is not shown.</li>
        </ul>
    <li>
        <a href="print_tableau.htm">print_tableau</a>
        <ul>
			<li>lpsolve('print_tableau', lp)

			<li>First call set_outputfile to specify where the information is written to.
			    In the API documentation it is written that by default, the output goes to stdout, but under PHP (Windows) this means that the output is not shown.</li>
        </ul>
    <li>
        <a href="put_abortfunc.htm">put_abortfunc</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="put_logfunc.htm">put_logfunc</a>
        <ul>
            <li>Not implemented.
            <li>However, the lpsolve driver sets a log function to redirect the output of lpsolve from stdout (which is not visible in Windows PHP) to the command window of PHP.
                As such, all reported output can be seen in PHP. How much output is seen is controlled by the verbose level that can be defined by set_verbose or can be specified in the read_ routines.</li>
        </ul>
    <li>
        <a href="put_msgfunc.htm">put_msgfunc</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="read_basis.htm">read_basis</a>
        <ul>
            <li>[ret, info] = lpsolve('read_basis', lp, filename)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="read_mps.htm">read_freemps, read_freeMPS</a>
        <ul>
            <li>lp_handle = lpsolve('read_freemps', filename {,
    options})
            <li>lp_handle = lpsolve('read_freeMPS', filename {,
    options})
            <li>In the lpsolve API, read_freemps needs a FILE
    handle. In PHP it needs the filename and thus acts the same as
    read_freeMPS.
            <li>lp_handle is not a pointer to an lprec structure as
    in the API, but an incrementing handle number starting from 0.
            <li>options is optional. If not specified, then NORMAL is used.</li>
        </ul>
    <li>
        <a name="read_lp"></a>
        <a href="read_lp.htm">read_lp, read_LP</a>
        <ul>
			<li>lp_handle = lpsolve('read_lp', filename {,
    verbose {, lp_name}})
            <li>lp_handle = lpsolve('read_LP', filename {,
    verbose {, lp_name}})
            <li>In the lpsolve API, read_lp needs a FILE handle. In
    PHP it needs the filename and thus acts the same as read_LP.
            <li>lp_handle is not a pointer to an lprec structure as
    in the API, but an incrementing handle number starting from 0.
            <li>verbose is optional. If not provided then NORMAL is
    used.
            <li>lp_name is optional. If not provided then no name is given to the model ('').</li>
        </ul>
    <li>
        <a href="read_MPS.htm">read_mps, read_MPS</a>
        <ul>
			<li>lp_handle = lpsolve('read_mps', filename {,
    options})
            <li>lp_handle = lpsolve('read_MPS', filename {,
    options})
            <li>In the lpsolve API, read_mps needs a FILE handle.
    In PHP it needs the filename and thus acts the same as read_MPS.
            <li>lp_handle is not a pointer to an lprec structure as
    in the API, but an incrementing handle number starting from 0.
            <li>options is optional. If not specified, then NORMAL is used.</li>
        </ul>
    <li>
        <a href="read_params.htm">read_params</a>
        <ul>
            <li>return = lpsolve('read_params', lp, filename {, options })
            <li>options is optional.</li>
        </ul>
    <li>
        <a href="read_XLI.htm">read_XLI</a>
        <ul>
            <li>lp_handle = lpsolve('read_XLI', xliname,
    modelname {, dataname {, options {, verbose}}}
            <li>lp_handle is not a pointer to an lprec structure as
    in the API, but an incrementing handle number starting from 0.
            <li>dataname is optional. When not provided, '' (NULL)
    is taken. '' is taken as NULL.
            <li>options is optional. When not provided, '' is
    taken.
            <li>verbose is optional. If not specified, then NORMAL is used.</li>
        </ul>
    <li>
        <a href="reset_basis.htm">reset_basis</a>
        <ul>
            <li>Not implemented.
            <li>Use <A href="default_basis.htm">default_basis</A></li>
        </ul>
    <li>
        <a href="set_basisvar.htm">set_basisvar</a>
        <ul>
            <li>lpsolve('set_basisvar', lp, basisPos, enteringCol)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_add_rowmode.htm">set_add_rowmode</a>
        <ul>
            <li>return = lpsolve('set_add_rowmode', lp,
    turnon)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_anti_degen.htm">set_anti_degen</a>
        <ul>
            <li>lpsolve('set_anti_degen', lp, anti_degen)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_basis.htm">set_basis</a>
        <ul>
            <li>return = lpsolve('set_basis', lp, [bascolumn], nonbasic)
            <li>In the API, element 0 of <i>bascolumn</i> is not used and values start from element 1. In PHP, there is no unused element in the matrix.</li>
        </ul>
    <li>
        <a href="set_basiscrash.htm">set_basiscrash</a>
        <ul>
            <li>lpsolve('set_basiscrash', lp, mode)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_bb_depthlimit.htm">set_bb_depthlimit</a>
        <ul>
            <li>lpsolve('set_bb_depthlimit', lp,
    bb_maxlevel)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_bb_floorfirst.htm">set_bb_floorfirst</a>
        <ul>
            <li>lpsolve('set_bb_floorfirst', lp,
    bb_floorfirst)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_bb_rule.htm">set_bb_rule</a>
        <ul>
            <li>lpsolve('set_bb_rule', lp, bb_rule)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_BFP.htm">set_BFP</a>
        <ul>
            <li>return = lpsolve('set_BFP', lp, filename)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_binary.htm">set_binary</a>
        <ul>
            <li>return = lpsolve('set_binary', lp, column,
    must_be_bin)
            <li>return = lpsolve('set_binary', lp,
    [must_be_bin])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all variables.</li>
        </ul>
    <li>
        <a href="set_bounds.htm">set_bounds</a>
        <ul>
            <li>return = lpsolve('set_bounds', lp, column,
    lower, upper)
            <li>return = lpsolve('set_bounds', lp,
    [lower], [upper])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all variables.</li>
        </ul>
    <li>
        <a href="set_bounds_tighter.htm">set_bounds_tighter</a>
        <ul>
            <li>lpsolve('set_bounds_tighter', lp, tighten)

            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_break_at_first.htm">set_break_at_first</a>
        <ul>
            <li>lpsolve('set_break_at_first', lp,
    break_at_first)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_break_at_value.htm">set_break_at_value</a>
        <ul>
            <li>lpsolve('set_break_at_value', lp,
    break_at_value)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_col_name.htm">set_col_name</a>
        <ul>
            <li>return = lpsolve('set_col_name', lp,
    column, name)
            <li>return = lpsolve('set_col_name', lp,
    [names])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all variables.</li>
        </ul>
    <li>
        <a href="set_column.htm">set_column, set_columnex</a>
        <ul>
            <li>return = lpsolve('set_column', lp, col_no,
    [column])
            <li>return = lpsolve('set_columnex', lp,
    col_no, [column])
            <li>Both have the same interface from <a href="set_column.htm">set_column</a> but act as <a href="set_column.htm">set_columnex</a></li>
        </ul>
    <li>
        <a href="set_constr_type.htm">set_constr_type</a>
        <ul>
            <li>return = lpsolve('set_constr_type', lp,
    row, con_type)
            <li>return = lpsolve('set_constr_type', lp,
    [con_type])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all rows.</li>
        </ul>
    <li>
        <a href="set_debug.htm">set_debug</a>
        <ul>
            <li>lpsolve('set_debug', lp, debug)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_epsb.htm">set_epsb</a>
        <ul>
            <li>lpsolve('set_epsb', lp, epsb)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_epsd.htm">set_epsd</a>
        <ul>
            <li>lpsolve('set_epsd', lp, epsd)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_epsel.htm">set_epsel</a>
        <ul>
            <li>lpsolve('set_epsel', lp, epsel)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_epsint.htm">set_epsint</a>
        <ul>
            <li>lpsolve('set_epsint', lp, epsint)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_epslevel.htm">set_epslevel</a>
        <ul>
            <li>lpsolve('set_epslevel', lp, epslevel)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_epsperturb.htm">set_epsperturb</a>
        <ul>
            <li>lpsolve('set_epsperturb', lp, epsperturb)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_epspivot.htm">set_epspivot</a>
        <ul>
            <li>lpsolve('set_epspivot', lp, epspivot)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_unbounded.htm">set_free</a>
        <a href="set_unbounded.htm">set_unbounded</a>
        <ul>
            <li>return = lpsolve('set_free', lp, column)
            <li>return = lpsolve('set_unbounded', lp, column)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_improve.htm">set_improve</a>
        <ul>
            <li>lpsolve('set_improve', lp, improve)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_infinite.htm">set_infinite</a>
        <ul>
            <li>lpsolve('set_infinite', lp, infinite)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_int.htm">set_int</a>
        <ul>
            <li>return = lpsolve('set_int', lp, column,
    must_be_int)
            <li>return = lpsolve('set_int', lp,
    [must_be_int])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all variables.</li>
        </ul>
    <li>
        <a href="set_lowbo.htm">set_lowbo</a>
        <ul>
            <li>return = lpsolve('set_lowbo', lp, column,
    value)
            <li>return = lpsolve('set_lowbo', lp,
    [values])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all variables.</li>
        </ul>
    <li>
        <a name="set_lp_name"></a>
        <a href="set_lp_name.htm">set_lp_name</a>
        <ul>
            <li>return = lpsolve('set_lp_name', lp, name)
            <li>In PHP, when you name a model, this name can be used everywhere where lp is specified.
                This to access the model via the name instead of via a handle.</li>
        </ul>
    <li>
        <a href="set_mat.htm">set_mat</a>
        <ul>
            <li>return = lpsolve('set_mat', lp, row,
    column, value)
            <li>return = lpsolve('set_mat', lp, [matrix])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows to set the whole matrix (all rows/columns) at once.
                This is the most performant way to provide the constraint matrix. Consider using a PHP sparse matrix for maximum performance and least memory usage.
                The matrix must be two-dimensional.</li>
        </ul>
    <li>
        <a href="set_maxim.htm">set_maxim</a>
        <ul>
            <li>lpsolve('set_maxim', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_maxpivot.htm">set_maxpivot</a>
        <ul>
            <li>lpsolve('set_maxpivot', max_num_inv)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_minim.htm">set_minim</a>
        <ul>
            <li>lpsolve('set_minim', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_mip_gap.htm">set_mip_gap</a>
        <ul>
            <li>lpsolve('set_mip_gap', lp, absolute,
    mip_gap)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_negrange.htm">set_negrange</a>
        <ul>
            <li>lpsolve('set_negrange', negrange)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_obj_fn.htm">set_obj</a>
        <ul>
            <li>return = lpsolve('set_obj', lp, column,
    value)
            <li>return = lpsolve('set_obj', lp, [values])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all variables. It is then the same as set_obj_fn</li>
        </ul>
    <li>
        <a href="set_obj_bound.htm">set_obj_bound</a>
        <ul>
            <li>lpsolve('set_obj_bound', lp, obj_bound)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_obj_fn.htm">set_obj_fn, set_obj_fnex</a>
        <ul>
            <li>return = lpsolve('set_obj_fn', lp, [row])
            <li>return = lpsolve('set_obj_fnex', lp,
    [row])
            <li>Both have the same interface from <a href="set_obj_fn.htm">set_obj_fn</a> but act as <a href="set_obj_fn.htm">set_obj_fnex</a>
            <li>In the API, element 0 is not used and values start from element 1. In PHP, there is no unused element in the matrix.</li>
        </ul>
    <li>
        <a href="set_output.htm">set_outputfile</a>
        <ul>
            <li>return = lpsolve('set_outputfile', lp,
    filename)
            <li>In the API description it says that setting filename to NULL results in writing output back to stdout.
                In PHP under Windows, output to stdout it not shown. However it results in closing the file.
                Use '' to have the effect of NULL.</li>
        </ul>
    <li>
        <a href="set_output.htm">set_outputstream</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="set_pivoting.htm">set_pivoting</a>
        <ul>
            <li>lpsolve('set_pivoting', lp, pivoting)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_preferdual.htm">set_preferdual</a>
        <ul>
            <li>lpsolve('set_preferdual', lp, dodual)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_presolve.htm">set_presolve</a>
        <ul>
            <li>lpsolve('set_presolve', lp, do_presolve {, maxloops})
            <li>The <i>maxloops</i> argument is optional in PHP. If not provided, then infinite is used.</li>
        </ul>
    <li>
        <a href="set_print_sol.htm">set_print_sol</a>
        <ul>
            <li>lpsolve('set_print_sol', lp, print_sol)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_rh.htm">set_rh</a>
        <ul>
            <li>return = lpsolve('set_rh', lp, row, value)

            <li>return = lpsolve('set_rh', lp, [values])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all rows. Note that in this case, the value of row 0 is not specified in the matrix.</li>
        </ul>
    <li>
        <a href="set_rh_range.htm">set_rh_range</a>
        <ul>
            <li>return = lpsolve('set_rh_range', lp, row,
    deltavalue)
            <li>return = lpsolve('set_rh_range', lp,
    [deltavalues])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all rows.</li>
        </ul>
    <li>
        <a href="set_rh_vec.htm">set_rh_vec</a>
        <ul>
            <li>lpsolve('set_rh_vec', lp, [rh])
            <li>In the API, element 0 is not used and values start from element 1. In PHP, there is no unused element in the matrix.</li>
        </ul>
    <li>
        <a href="set_row.htm">set_row, set_rowex</a>
        <ul>
            <li>return = lpsolve('set_row', lp, row_no,
    [row])
            <li>return = lpsolve('set_rowex', lp, row_no,
    [row])
            <li>Both have the same interface from <a href="set_row.htm">set_row</a> but act as <a href="set_row.htm">set_rowex</a>
            <li>In the API, element 0 is not used and values start from element 1. In PHP, there is no unused element in the matrix.</li>
        </ul>
    <li>
        <a href="set_row_name.htm">set_row_name</a>
        <ul>
            <li>return = lpsolve('set_row_name', lp, row,
    name)
            <li>return = lpsolve('set_row_name', lp,
    [names])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all rows.</li>
        </ul>
    <li>
        <a href="set_scalelimit.htm">set_scalelimit</a>
        <ul>
            <li>lpsolve('set_scalelimit', lp, scalelimit)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_scaling.htm">set_scaling</a>
        <ul>
            <li>lpsolve('set_scaling', lp, scalemode)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_semicont.htm">set_semicont</a>
        <ul>
            <li>return = lpsolve('set_semicont', lp,
    column, must_be_sc)
            <li>return = lpsolve('set_semicont', lp,
    [must_be_sc])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all variables.</li>
        </ul>
    <li>
        <a href="set_sense.htm">set_sense</a>
        <ul>
            <li>lpsolve('set_sense', lp, maximize)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_simplextype.htm">set_simplextype</a>
        <ul>
            <li>lpsolve('set_simplextype', lp,
    simplextype)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_solutionlimit.htm">set_solutionlimit</a>
        <ul>
            <li>lpsolve('set_solutionlimit', lp,
    simplextype)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_timeout.htm">set_timeout</a>
        <ul>
            <li>lpsolve('set_timeout', lp, sectimeout)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_trace.htm">set_trace</a>
        <ul>
            <li>lpsolve('set_trace', lp, trace)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_upbo.htm">set_upbo</a>
        <ul>
            <li>return = lpsolve('set_upbo', lp, column,
    value)
            <li>return = lpsolve('set_upbo', lp, [values])

            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all variables.</li>
        </ul>
    <li>
        <a href="set_use_names.htm">set_use_names</a>
        <ul>
            <li>lpsolve('set_use_names', lp, isrow, use_names)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_var_branch.htm">set_var_branch</a>
        <ul>
            <li>return = lpsolve('set_var_branch', lp,
    column, branch_mode)
            <li>return = lpsolve('set_var_branch', lp,
    [branch_mode])
            <li>In PHP, this routine has two formats. The first format is identical to the API.
                The second format allows setting a matrix of all variables.</li>
        </ul>
    <li>
        <a href="set_var_weights.htm">set_var_weights</a>
        <ul>
            <li>return = lpsolve('set_var_weights', lp,
    [weights])
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_verbose.htm">set_verbose</a>
        <ul>
            <li>lpsolve('set_verbose', lp, verbose)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="set_XLI.htm">set_XLI</a>
        <ul>
            <li>return = lpsolve('set_XLI', lp, filename)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="solve.htm">solve</a>
        <ul>
            <li>result = lpsolve('solve', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="add_column.htm">str_add_column</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="add_constraint.htm">str_add_constraint</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="set_obj_fn.htm">str_set_obj_fn</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="set_rh_vec.htm">str_set_rh_vec</a>
        <ul>
            <li>Not implemented.</li>
        </ul>
    <li>
        <a href="time_elapsed.htm">time_elapsed</a>
        <ul>
            <li>return = lpsolve('time_elapsed', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="unscale.htm">unscale</a>
        <ul>
            <li>lpsolve('unscale', lp)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="write_basis.htm">write_basis</a>
        <ul>
            <li>lpsolve('write_basis', lp, filename)
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="write_mps.htm">write_freemps, write_freeMPS</a>
        <ul>
            <li>return = lpsolve('write_freemps', lp,
    filename)
            <li>return = lpsolve('write_freeMPS', lp,
    filename)
            <li>In the lpsolve API, write_freeMPS needs a FILE handle. In PHP it needs the filename and thus acts the same as write_freemps.</li>
        </ul>
    <li>
        <a href="write_lp.htm">write_lp, write_LP</a>
        <ul>
            <li>return = lpsolve('write_lp', lp, filename)

            <li>return = lpsolve('write_LP', lp, filename)

            <li>In the lpsolve API, write_LP needs a FILE handle. In PHP it needs the filename and thus acts the same as write_lp.</li>
        </ul>
    <li>
        <a href="write_mps.htm">write_mps, write_MPS</a>
        <ul>
            <li>return = lpsolve('write_mps', lp,
    filename)
            <li>return = lpsolve('write_MPS', lp,
    filename)
            <li>In the lpsolve API, write_MPS needs a FILE handle.
    In PHP it needs the filename and thus acts the same as write_mps.
            <li>No special considerations.</li>
        </ul>
    <li>
        <a href="write_XLI.htm">write_XLI</a>
        <ul>
            <li>return = lpsolve('write_XLI', lp, filename
    {, options {, results}})
            <li>No special considerations.</li>
        </ul>
    </li>
</ul>

<a name="Extra_PHP_routines"></a>
<h3>Extra PHP routines</h3>

<p>These routines are not part of the lpsolve API, but are added for backwards compatibility.
Most of them exist in the lpsolve API with another name.</p>

<ul>
	<li>[names] = lpsolve('get_col_names', lp)
        <ul>
            <li>The same as get_col_name. Implemented for backwards compatibility.</li>
        </ul>
	<li>[constr_type] = lpsolve('get_constr_types', lp)
        <ul>
            <li>The same as get_constr_type. Implemented for backwards compatibility.</li>
        </ul>
    <li>[int] = lpsolve('get_int', lp)
        <ul>
            <li>The same as is_int. Implemented for backwards compatibility.</li>
        </ul>
    <li>return = lpsolve('get_no_cols', lp)
        <ul>
            <li>The same as get_Ncolumns. Implemented for backwards compatibility.</li>
        </ul>
    <li>return = lpsolve('get_no_rows', lp)
        <ul>
            <li>The same as get_Nrows. Implemented for backwards compatibility.</li>
        </ul>
    <li>name = lpsolve('get_objective_name', lp)
        <ul>
            <li>The same as get_row_name with row=0. Implemented for backwards compatibility.</li>
        </ul>
    <li>[row_vec, return] = lpsolve('get_obj_fn', lp)<br>
        [row_vec, return] =
           lpsolve('get_obj_fun', lp)
        <ul>
            <li>The same as get_row with row 0. Implemented for backwards compatibility.</li>
        </ul>
    <li>name = lpsolve('get_problem_name', lp)
        <ul>
            <li>The same as get_lp_name. Implemented for backwards compatibility.</li>
        </ul>
    <li>[costs] = lpsolve('get_reduced_costs', lp)
        <ul>
            <li>The same as get_dual_solution. Implemented for backwards compatibility.</li>
        </ul>
	<li>[names] = lpsolve('get_row_names', lp)
        <ul>
            <li>The same as get_row_name. Implemented for backwards compatibility.</li>
        </ul>
    <li>[obj, x, duals, return] = lpsolve('get_solution', lp)
        <ul>
            <li>Returns the value of the objective function, the
    values of the variables and the duals. Implemented for backwards
    compatibility.
            <li>The return code of the call is the last value.</li>
        </ul>
    <li>value = lpsolve('mat_elm', lp)
        <ul>
            <li>The same as get_mat. Implemented for backwards compatibility.</li>
        </ul>
    <li>[handle_vec] = lpsolve('print_handle')
        <ul>
            <li>Returns a vector with open handles.
                Can be handy to see which handles aren't closed yet with delete_lp or free_lp.</li>
        </ul>
    <li>lp_handle = lpsolve('read_lp_file', filename {, verbose {, lp_name}})
        <ul>
            <li>The same as read_LP. Implemented for backwards compatibility.</li>
        </ul>
    </li>
    <li><a name="get_handle"></a>lp_handle = lpsolve('get_handle', lp_name)
        <ul>
            <li>Get the handle for this model from the models name.
                If an unknown model name is given (or already deleted), -1 is returned.
            </li>
       </ul>
    </li>
    <li><a name="return_constants"></a>return_constants = lpsolve('return_constants'[, return_constants])
        <ul>
            <li>Returns the setting of return_constants and optionally sets its value.
            </li>
       </ul>
    </li>
</ul>

<a name="Compile_the_lpsolve_driver"></a>
<h3>Compile the lpsolve driver</h3>

<p>The lpsolve PHP driver is called php_phplpsolve55.dll (windows) and phplpsolve55.so (Unix/Linux).<br>
This driver is an interface to the lpsolve55.dll (windows) and liblpsolve55.so (Unix/Linux) lpsolve shared library that contains the implementation of lp_solve.
lpsolve55.dll/liblpsolve55.so is distributed with the lp_solve package (archive lp_solve_5.5.0.15_dev.zip/lp_solve_5.5.0.15_dev.tar.gz). The lpsolve PHP driver is just
a wrapper between PHP and lp_solve to translate the input/output to/from PHP and the lp_solve library.
</p>

<p>The lpsolve PHP driver is written in C. To compile this code a C compiler is needed.
Under Unix, this is the standard C compiler (c/gcc) and under windows it is the Microsoft compiler from Visual Studio .NET.
</p>

<h4>Windows:</h4>
<p>First of all, the PHP sources are needed. These must be obtained from <a href="http://www.php.net">http://www.php.net</a><br>
Extract the sources to a folder. In this example this is f:\php-5.2.6<br>
Secondly, win32build.zip is needed. Note that is looks like this is only the case if method 1) is used. Method 2) works without this folder.
This archive should be on that site also, but apparently it is deleted from there.
It was however found on following location: <a href="http://viewcvs.php.net/viewvc.cgi/phpweb/extra/?hideattic=0">http://viewcvs.php.net/viewvc.cgi/phpweb/extra/?hideattic=0</a><br>
Extract the sources to \win32build. In this example this is f:\win32build<br>
</p>
<p>Then there are two ways to compile php_phplpsolve.dll:</p>

<p>1) This is somewhat the standard way, but not the easiest.<br>
First of all, lpsolve must be under the php\ext folder. For example \php-5.2.6\ext\lpsolve<br>
Secondly, a file config.w32 is needed. It is provided with the lpsolve PHP distribution.<br>
To build, execute the following commands in a DOS prompt where the vcvars32.bat file of the Microsoft Visual C compiler:
</p>

<pre>
F:\php-5.2.6&gt;buildconf.bat
F:\php-5.2.6&gt;cscript /nologo configure.js --without-xml --without-wddx --without-simplexml --without-dom --without-libxml --disable-zlib --without-sqlite --disable-odbc --disable-cgi --enable-cli --without-iconv --enable-phplpsolve55=shared --with-phplpsolve55path="z:\lp_solve_5.5"
F:\php-5.2.6&gt;nmake php_phplpsolve55.dll
</pre>

<p>Each of these commands give some messages. Also note that in the cscript command, the path to lp_solve_5.5 must be provided.
In this example this is z:\lp_solve_5.5
</p>

<p>If all is successful, php_phplpsolve.dll is made in \php-5.2.6\Release_TS
</p>

<p>2) An easier way is using the batch files cvc6.bat or cvc8NOmsvcrt80.bat<br>
cvc6.bat can be used for Microsoft Visual compiler version 6 and cvc8NOmsvcrt80.bat for Microsoft Visual compilers from .NET.<br>
In this case, the lpsolve PHP files do not have to be located under \php-5.2.6\ext. Instead they can be under
\lp_solve_5.5\extra\PHP\lpsolve as in the distribution.<br>
First edit cvc6.bat or cvc8NOmsvcrt80.bat<br>
Two commands here must be revised:
</p>

<pre>
set lp_solve=..\..
set php=F:\php-5.2.6
</pre>

<p>
Normally the first one will be ok. The second one must be changed to the path where the PHP sources are located. Here F:\php-5.2.6
</p>

<p>
To build, execute the following commands in a DOS prompt where the vcvars32.bat file of the Microsoft Visual C compiler:
</p>

<pre>
Z:\lp_solve_5.5\extra\PHP&gt;cvc6.bat
</pre>

or

<pre>
Z:\lp_solve_5.5\extra\PHP&gt;cvc8NOmsvcrt80.bat
</pre>

<h4>Unix/Linux:</h4>
<p>Go to the lpsolve PHP directory and enter the following commands:
</p>

<pre>
$ phpize
$ ./configure --enable-maintainer-zts --with-phplpsolve55=../..
$ make
</pre>

<p>
Note the ../.. path in the second command. That is the location to lp_solve_5.5 and that is normally 2 directories down.
</p>

<p>After this is done, phplpsolve55.so is build in directory modules.
</p>

<p>For both windows and unix/linux, don't forget to adapt php.ini as described above and possibly restart the webserver.
</p>

<p>See also <a href="MATLAB.htm">Using lpsolve from MATLAB</a>,
            <a href="O-Matrix.htm">Using lpsolve from O-Matrix</a>,
            <a href="Sysquake.htm">Using lpsolve from Sysquake</a>,
            <a href="Scilab.htm">Using lpsolve from Scilab</a>,
            <a href="Octave.htm">Using lpsolve from Octave</a>,
            <a href="FreeMat.htm">Using lpsolve from FreeMat</a>,
            <a href="Euler.htm">Using lpsolve from Euler</a>,
            <a href="Python.htm">Using lpsolve from Python</a>
            <a href="Sage.htm">Using lpsolve from Sage</a>,
            <a href="R.htm">Using lpsolve from R</a>
</p>
	</BODY>
</html>