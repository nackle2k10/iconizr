iconizr
=======
is an – at present: proof-of-concept – **command line tool** that helps you prepare your vector based SVG icons for use with the widest possible range of devices. It takes a [folder of SVG files](example/weather) and processes them to a bunch of files including

*	cleaned versions of the original **SVG icons** (optional),
*	a single compact **[SVG icon sprite](example/css/weather-16123200/weather-16123200.svg)**,
*	single **PNG icons** (optional),
*	a combined **[PNG icon sprite](example/css/weather-16123200/weather-16123200.png)**,
*	several **CSS files** with different formats inlcuding
	*	[SVG single image icons](example/css/weather-svg-single.css) (optional),
	*	[SVG data URIs](example/css/weather-svg-data.css),
	*	[SVG sprite references](example/css/weather-svg-sprite.css),
	*	[PNG single image icons](example/css/weather-png-single.css) (optional),
	*	[PNG data URIs](example/css/weather-png-data.css) and
	*	[PNG sprite references](example/css/weather-png-sprite.css),
*	**[Sass variants](example/sass)** of these CSS files for easy inclusion into your Sass project,
*	an **[HTML fragment](example/css/weather-loader-fragment.html)** including some JavaScript for asynchronously loading the most appropriate icon variant
*	and finally an **[HTML/PHP preview page](example/css/weather-preview.php)** for testing the different icon variants. 

To leverage *iconizr* to the greatest extent possible, some additional tools have to be installed on the system independently ([see below](#requirements)).  

*iconizr* is written in (namespaced) **PHP** and meant to be used on the (Linux) command line (i.e. with the PHP CLI SAPI). To be honest, there was only one reason making me go for PHP: It is what I know best. Im sure there are quite some other fancy modern languages and techniques one could use for achieving the same, but I guess I'm just not smart enough for those. Having said this, I'd welcome anyone contributing a port of *iconizr*.

Getting started
---------------

As *iconizr* is written in PHP, there's nothing much of an installation process. Just put the `iconizr.phps` script (along with it's accompanying files and directories) to a location of your liking and make sure that it may be executed by setting the proper file permissions. If you'd like to simplify the invocation of iconizr you might create a symlink  into your local executables directory, e.g.:

	ln -s /path/to/iconizr.phps /usr/local/bin/iconizr
	
Run *iconizr* by calling the script with the apropriate command line options ([see below](#documentation)). Please consider that – for security reasons – the output directory has to be a sub(sub)directory of the current working directory, so change your working directory first if necessary:

	cd /path/to/website/docroot
	/path/to/iconizr.phps --css --out css source/svg

or using the symlink mentioned above:

	iconizr --css --out css source/svg

This will take all the SVG files found in `/path/to/website/docroot/source/svg` and render the resulting CSS files and sprites to the directory `/path/to/website/docroot/css` (and below). For the sake of simplicity, Sass files have been omitted in this example.

Documentation
-------------

Usage:

	iconizr [options] input/dir1 [input/dir2 ...]

<table>
	<tr>
		<td>Short</td>
		<td>Long</td>
		<td>Value</td>
		<td>Description</td>
	</tr>
	<tr>
		<td><code>-o</code></td>
		<td><code>--out</code></td>
		<td>Directory path</td>
		<td>Provide the path of the output directory here. For security reasons, the output directory has to be a sub(sub)directory of the current working directory. If it doesn't exist, iconizr will try to create it. Along with the input directory this is a mandatory argument.</td>
	</tr>
	<tr>
		<td> </td>
		<td><code>--sassout</code></td>
		<td>Directory path</td>
		<td>If you want the Sass files to be created in a separate directory, then specify it using this argument. The same restrictions apply as for the standard output directory. If you don't specify this argument, the Sass files will be created in the standard output directory.</td>
	</tr>
	<tr>
		<td><code>-c</code></td>
		<td><code>--css</code></td>
		<td>String (optional)</td>
		<td>If this argument is given, several CSS files will be generated. By default, the filenames will all start with "iconizr" (e.g. "iconizr-svg-data.css"). If you provide a string value for this argument (e.g. "myproject"), this value will be used as the prefix for all generated CSS files (e.g. "myproject-svg-data.css").</td>
	</tr>
	<tr>
		<td><code>-s</code></td>
		<td><code>--sass</code></td>
		<td>String (optional)</td>
		<td>Identical to the <code>-c</code> argument, but applying to the generation of Sass files.</td>
	</tr>
	<tr>
		<td><code>-p</code></td>
		<td><code>--prefix</code></td>
		<td>String</td>
		<td>This is the prefix used for all CSS class names generated. It defaults to "icon", so that a SVG file named <code>example.svg</code> results in a CSS class named "icon-example".</td>
	</tr>
	<tr>
		<td><code>-l</code></td>
		<td><code>--level</code></td>
		<td>Integer</td>
		<td>This is the optimization level for PNG files. It has to lie between 0 and 11 (inclusive) and defaults to 4, with 0 meaning "no optimization", 1 meaning "fast & rough" and 11 meaning "slow & high quality". Setting this to a high value may result in a very long processing time.</td>
	</tr>
	<tr>
		<td><code>-q</code></td>
		<td><code>--quantize</code></td>
		<td> </td>
		<td>If given, iconizr will quantize PNG images (i.e. convert them to 8-bit color depth; please see the <a href="#requirements">requirements</a> for this). The quantized images are only used if they are smaller in file size than their the originals (and this is not necessarily the case for all PNG files). Quantization may also impact the visual image quality, so please make sure to compare the result to the original images.</td>
	</tr>
	<tr>
		<td><code>-r</code></td>
		<td><code>--root</code></td>
		<td> </td>
		<td>If given, iconizr will use the CSS output directory as root-relative path in the stylesheet loader HTML fragment (i.e. <code>/path/to/icons.css</code> instead of <code>path/to/icons.css</code>).</td>
	</tr>
	<tr>
		<td> </td>
		<td><code>--svg</code></td>
		<td>Integer</td>
		<td>This is the maximum length a SVG data URI may have. If only one icon exceeds this threshold, all data URIs of this icon set will be changed to external SVG sprite references. Defaults to 1048576 (1MB), minimum is 1024 (1kB).</td>
	</tr>
	<tr>
		<td> </td>
		<td><code>--png</code></td>
		<td>Integer</td>
		<td>This is the maximum length a PNG data URI may have. If only one icon exceeds this threshold, all data URIs of this icon set will be changed to external PNG sprite references. Defaults to 32768 (32KB = Internet Explorer 8 limit), minimum is 1024 (1kB).</td>
	</tr>
	<tr>
		<td><code>-d</code></td>
		<td><code>--dims</code></td>
		<td> </td>
		<td>If given, a second CSS rule for each icon will be created, setting the dimensions of the icon (via the <code>width</code> and <code>height</code> properties).</td>
	</tr>
	<tr>
		<td> </td>
		<td><code>--width</code></td>
		<td>Integer</td>
		<td>Default width for icons (in pixel) in case the SVG file is missing a value. Must be between 1 and 1000 (including).</td>
	</tr>
	<tr>
		<td> </td>
		<td><code>--height</code></td>
		<td>Integer</td>
		<td>Default height for icons (in pixel) in case the SVG file is missing a value. Must be between 1 and 1000 (including).</td>
	</tr>
	<tr>
		<td><code>-k</code></td>
		<td><code>--keep</code></td>
		<td> </td>
		<td>During the compilation of the SVG and PNG sprites, iconizr also creates intermediate SVG and PNG versions of each single icon. Usually, these intermediate files are deleted at the end of the compilation process. If you want to keep these files for some reason, just add this argument.<br/>In this case, also an additional set of stylesheets will be created (both CSS and/or Sass), which uses these single image icons (carrying the suffix <code>-single.html</code>). However, it is not recommended to use these stylesheets for production systems, and you should always prefer the CSS sprite or data URI variants for performance reasons. The single image icon stylesheets will also be ignored by the stylesheet loader JavaScript, but at least you will be able to select them explicitly using the icon kit preview documents.</td>
	</tr>
	<tr>
		<td><code>-v</code></td>
		<td><code>--verbose</code></td>
		<td>Integer (optional)</td>
		<td>By default, iconizr operates quietly without any output. If you specify this argument (optionally using the value "1"), some simple status messages will be printed. Use "2" as value to even print the console output of the external tools being used (like e.g. <code>pngcrush</code> or <code>pngquant</code>).</td>
	</tr>
	<tr>
		<td> </td>
		<td><code>--scour</code></td>
		<td>String</td>
		<td>iconizr can use either <a href="https://github.com/svg/svgo">SVGO</a> or <a href="http://www.codedread.com/scour">Scour</a> for cleaning the original SVG files. As far as I can tell, Scour yields slightly better results (i.e. smaller SVG files) than SVGO. As Scour is rather a Python script than an executable, you need to provide the full script path via this option if you want to use it (also, of course, Python has to be available on the system; see <a href="#requirements">requirements</a>).</td>
	</tr>
	<tr>
		<td> </td>
		<td><code>--python</code></td>
		<td>String</td>
		<td>When using <a href="http://www.codedread.com/scour">Scour</a> for cleaning the original SVG files, you need to have <a href="http://www.python.org" target="_blank">Python 2</a> handy, as Scour is based on this version and hasn't been updated for quite a while. If your default Python binary is not version 2, you need to specify the absolute path to an alternative version 2 binary with this parameter.</td>
	</tr>
</table>

Examples
--------

Comparison to grunticon
-----------------------
While doing pretty much the same as the Filament Group's / Scott Jehl's [grunticon](https://github.com/filamentgroup/grunticon), *iconizr* especially focuses on reducing the size of files and number of HTTP requests, addressing mobile devices in particular:

1.	SVG files are cleaned and freed from a lot of cruft typically introduced by SVG editing application before they get converted to data URIs or embedded into the SVG sprite.
2.	PNG files are losslessly optimized (and optionally quantized to 8-bit files) before being used in data URIs or the PNG sprite.
3.	As soon as **even one of the icons** needs to be loaded externally (due to exceeding a potential data URI size limitation), **all icons** will get loaded via the corresponding sprite.  


Evtl. kein Limit für data-URIs bei SVG? Welcher Browser mit SVG-Support hat das kleinste data-URI-Limit?

<table>
	<tr>
		<th colspan="2">Rendering mode</th>
		<th>grunticon</th>
		<th>iconizr</th>
	</tr>
	<tr>
		<td rowspan="2">PNG images / sprite</td>
		<td>Requests</td>
		<td>11</td>
		<td>2</td>
	</tr>
	<tr>
		<td>Size (KB)</td>
		<td>33.3 / 31.2</td>
		<td>20.0 / 21.1</td>
	</tr>
	<tr>
		<td rowspan="2">PNG data URI</td>
		<td>Requests</td>
		<td>1</td>
		<td>1</td>
	</tr>
	<tr>
		<td>Size (KB)</td>
		<td>30.1 / 41.1</td>
		<td>20.3 / 29.7</td>
	</tr>
	<tr>
		<td rowspan="2">SVG sprite</td>
		<td>Requests</td>
		<td>-</td>
		<td>2</td>
	</tr>
	<tr>
		<td>Size (KB)</td>
		<td>-</td>
		<td>42.8 / 496.0</td>
	</tr>
	<tr>
		<td rowspan="2">SVG data URI</td>
		<td>Requests</td>
		<td>1</td>
		<td>1</td>
	</tr>
	<tr>
		<td>Size (KB)</td>
		<td>126.0 / 1,913.0</td>
		<td>49.4 / 720.0</td>
	</tr>
</table>


Requirements
------------
For iconizr to run, you will need some programs installed on your machine. These are:

*	[PHP](http://php.net) (kinda obvious)
*	[PhantomJS](http://phantomjs.org) (for rendering PNG images of your SVG drawings)

For SVG optimization you will need either of the following (or both):

*	[SVGO](https://github.com/svg/svgo) (which needs [Node.js](http://nodejs.org) ...) 
*	[Scour](http://www.codedread.com/scour) (which needs [Python 2](http://www.python.org) ...)

For PNG optimization you should have as many of the following as possible:

*	[Pngcrush](http://pmt.sourceforge.net/pngcrush)
*	[pngquant](http://pngquant.org)
*	[OptiPNG](http://optipng.sourceforge.net)


Resources
---------
There are some useful resources on data URIs in general:

*	[Data URIs](https://developer.mozilla.org/en-US/docs/data_URIs)
*	[Data URI support](http://caniuse.com/datauri)
*	[Data URI limitation to 128kB on iPhone / Safari 3.0](http://blog.clawpaws.net/post/2007/07/16/Storing-iPhone-apps-locally-with-data-URLs#c1989348)
*	[Data URI limitation checker](http://odl-nbg.de/test/datauri.php) to test which data URI size is supported by your device

As soon as I'll find some time I will do some tests in our [Open Device Lab](http://odl-nbg.de) to see if there are any device specific data URI limitations. 

Resources on SVG support:

*	[Can I use SVG](http://caniuse.com/svg)


Legal
-----
*iconizr* by [Joschi Kuphal](http://joschi.kuphal.net) is licensed under a [Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/). Follow [@jkphl](http://twitter.com/jkphl) on Twitter to keep updated.

