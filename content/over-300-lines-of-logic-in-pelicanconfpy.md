Title: Permalink to Over 300 Lines of Logic in pelicanconf.py
Date: 03/09/2022
Status: Published
Slug: over-300-lines-of-logic-in-pelicanconfpy

I wrote 297 lines of python code, extending a pelican static site generator implementation &#8212; <em>this</em> one actually. And I crammed all of it into my <code>pelicanconf.py</code> file. because that is the only way I could get it to work! This post has been brought to you by the general awareness that you realize over time: &#8220;god help me, this file is a&nbsp;mess.&#8221;</p>
<hr>
<h4>If you already know what Pelican is, skip&nbsp;down</h4>
<h2>What is&nbsp;Pelican?</h2>
<p><a href="https://blog.getpelican.com/">Pelican</a> is the library that i use, if you didn&#8217;t know, to turn rambling nonsense typed into a notepad file into these pages automatically. Everything links to everything it should on other pages and if I add anything, all the old pages automatically are updated to stay in format with most current&nbsp;pages. </p>
<p>Pagination, content archives, categories, tags (although these are not fully implemented here currently), and all the other kind of website stuff you have to figure out somehow when you write something into your notepad if you expect it to suddenly become a web&nbsp;page.</p>
<p>What&#8217;s cool about pelican is that it is a type of website build tool called a <a href="">static site generator</a> I don&#8217;t have to maintain a server somewhere to dynamically render the page content for a users (who would all collectively have to wait for the server to figure out what to render to all of&nbsp;them).</p>
<h2>Static Sites vs Web&nbsp;Applications</h2>
<p>The main thing here is that instead of deciding what to show the user on a remote server, I&#8217;m doing it here on my home computer. When it goes to the server, there is nothing for the server to process and figure out in order to send something back to the user. It simply serves the page, as it&#8217;s already there to send. It doesn&#8217;t change. It&#8217;s a url path and a file that is associated with&nbsp;it.</p>
<p>The website is the same for everyone who uses it. It doesn&#8217;t show some users one thing and another user another thing (like their login information or their profile or something where that is not possible to just be a simple, static&nbsp;document).</p>
<p>But with a blog, unless you&#8217;ve lost an amount of integrity that is quite a bit more than the average web marketer loses by default in their career of being a scumbag with their approach to the Internet, you don&#8217;t have a reason to dynamically render a user experience of reading information. You just don&#8217;t. And if you do, you&nbsp;shouldn&#8217;t. </p>
<p>You simply publish the information. That is the best most, efficient way to serve it. It makes sure it&#8217;s accessible to more&nbsp;people.</p>
<hr>
<h2>Time For the __main__&nbsp;Event</h2>
<p>I know, you came for the 300+ line settings file (not the politics). So let&#8217;s get down to business. I warn you, take a moment to prepare yourself and maybe sit down. The following is actual&nbsp;madness.</p>
<p>I&#8217;m going to show you all the code before I explain what any of it does for&nbsp;effect.</p>
<p>Just know that this file is supposed to be a series of value definitions that are used as a settings file and nothing&nbsp;more.</p>
<p>&#8230;</p>
<p>This is a genre of post where the idea is to reveal intimately how embarrassing and disorganized some of your files can be.
Who knows, maybe it&#8217;s a step towards the solutions to those&nbsp;problems.</p>
<div class="codehilite"><pre><span></span><code><span class="kn">from</span> <span class="nn">pathlib</span> <span class="kn">import</span> <span class="n">Path</span>
<span class="kn">from</span> <span class="nn">string</span> <span class="kn">import</span> <span class="n">Template</span>
<span class="kn">from</span> <span class="nn">datetime</span> <span class="kn">import</span> <span class="n">datetime</span>
<span class="kn">from</span> <span class="nn">jinja2.environment</span> <span class="kn">import</span> <span class="n">Environment</span>
<span class="kn">from</span> <span class="nn">jinja2.filters</span> <span class="kn">import</span> <span class="n">environmentfilter</span>
<span class="kn">from</span> <span class="nn">sys</span> <span class="kn">import</span> <span class="n">exit</span>
<span class="kn">from</span> <span class="nn">typing</span> <span class="kn">import</span> <span class="n">Any</span><span class="p">,</span> <span class="n">Callable</span>


<span class="k">class</span> <span class="nc">PostDate</span><span class="p">:</span>
    <span class="k">def</span> <span class="fm">__init__</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">date</span><span class="o">=</span><span class="kc">None</span><span class="p">):</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">date</span> <span class="o">=</span> <span class="n">date</span> <span class="k">if</span> <span class="n">date</span> <span class="ow">is</span> <span class="ow">not</span> <span class="kc">None</span> <span class="k">else</span> <span class="n">datetime</span><span class="o">.</span><span class="n">now</span><span class="p">()</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">modified</span> <span class="o">=</span> <span class="kc">None</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">output_meta_str</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">get_output_metadata_str</span><span class="p">()</span>

    <span class="k">def</span> <span class="fm">__str__</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="k">return</span> <span class="sa">f</span><span class="s2">&quot;created: </span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">format_datetime</span><span class="p">()</span><span class="si">}</span><span class="s2">&quot;</span> <span class="k">if</span> <span class="bp">self</span><span class="o">.</span><span class="n">modified</span> <span class="ow">is</span> <span class="kc">None</span> <span class="k">else</span> \
            <span class="sa">f</span><span class="s2">&quot;created: </span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">format_datetime</span><span class="p">()</span><span class="si">}</span><span class="s2">, modified: </span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">format_datetime</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">modified</span><span class="p">)</span><span class="si">}</span><span class="s2">&quot;</span>

    <span class="k">def</span> <span class="fm">__repr__</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="k">return</span> <span class="bp">self</span><span class="o">.</span><span class="fm">__str__</span><span class="p">()</span>

    <span class="k">def</span> <span class="nf">handle_modified_time</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">new_mod</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        Pass the str &quot;init&quot; to clear modified time.</span>
<span class="sd">        Pass the str &quot;now&quot; to set new modified time to now.</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="n">new_mod</span> <span class="o">=</span> <span class="n">datetime</span><span class="o">.</span><span class="n">now</span><span class="p">()</span> <span class="k">if</span> <span class="n">new_mod</span> <span class="o">==</span> <span class="s2">&quot;now&quot;</span> <span class="k">else</span> <span class="n">new_mod</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">modified</span> <span class="o">=</span> <span class="kc">None</span> <span class="k">if</span> <span class="n">new_mod</span> <span class="o">==</span> <span class="s2">&quot;init&quot;</span> <span class="k">else</span> <span class="n">new_mod</span>

    <span class="nd">@staticmethod</span>
    <span class="k">def</span> <span class="nf">_format_time</span><span class="p">(</span><span class="n">time</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        Use format_datetime()</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="k">return</span> <span class="s2">&quot;</span><span class="si">{:02d}</span><span class="s2">:</span><span class="si">{:02d}</span><span class="s2">&quot;</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="n">time</span><span class="o">.</span><span class="n">hour</span><span class="p">,</span> <span class="n">time</span><span class="o">.</span><span class="n">minute</span><span class="p">)</span>

    <span class="nd">@staticmethod</span>
    <span class="k">def</span> <span class="nf">_format_date</span><span class="p">(</span><span class="n">date</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        Use format_datetime()</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="c1"># return f&quot;{date.year}-{date.month}-{date.day}&quot;</span>
        <span class="k">return</span> <span class="s2">&quot;</span><span class="si">{:04d}</span><span class="s2">-</span><span class="si">{:02d}</span><span class="s2">-</span><span class="si">{:02d}</span><span class="s2">&quot;</span><span class="o">.</span><span class="n">format</span><span class="p">(</span>
            <span class="n">date</span><span class="o">.</span><span class="n">year</span><span class="p">,</span> <span class="n">date</span><span class="o">.</span><span class="n">month</span><span class="p">,</span> <span class="n">date</span><span class="o">.</span><span class="n">day</span>
        <span class="p">)</span>

    <span class="k">def</span> <span class="nf">format_datetime</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">mod_date</span><span class="o">=</span><span class="kc">None</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        To add a modified date, pass mod_date = self.modified</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="k">return</span> <span class="sa">f</span><span class="s2">&quot;</span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">_format_date</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">date</span><span class="o">.</span><span class="n">date</span><span class="p">())</span><span class="si">}</span><span class="s2"> </span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">_format_time</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">date</span><span class="o">.</span><span class="n">time</span><span class="p">())</span><span class="si">}</span><span class="s2">&quot;</span> \
            <span class="k">if</span> <span class="n">mod_date</span> <span class="ow">is</span> <span class="kc">None</span> <span class="k">else</span> <span class="sa">f</span><span class="s2">&quot;</span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">_format_date</span><span class="p">(</span><span class="n">mod_date</span><span class="o">.</span><span class="n">date</span><span class="p">())</span><span class="si">}</span><span class="s2"> </span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">_format_time</span><span class="p">(</span><span class="n">mod_date</span><span class="o">.</span><span class="n">time</span><span class="p">())</span><span class="si">}</span><span class="s2">&quot;</span>

    <span class="k">def</span> <span class="nf">get_output_metadata_str</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="n">d</span> <span class="o">=</span> <span class="sa">f</span><span class="s2">&quot;Date: </span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">format_datetime</span><span class="p">()</span><span class="si">}</span><span class="se">\n</span><span class="s2">&quot;</span>
        <span class="k">return</span> <span class="n">d</span> <span class="k">if</span> <span class="bp">self</span><span class="o">.</span><span class="n">modified</span> <span class="ow">is</span> <span class="kc">None</span> <span class="k">else</span> \
            <span class="n">d</span> <span class="o">+</span> <span class="sa">f</span><span class="s2">&quot;Modified: </span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">format_datetime</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">modified</span><span class="p">)</span><span class="si">}</span><span class="s2">&quot;</span>


<span class="k">class</span> <span class="nc">NewPost</span><span class="p">:</span>
    <span class="n">DEFAULT_TITLE</span> <span class="o">=</span> <span class="s2">&quot;This needs a title&quot;</span>
    <span class="n">DEFAULT_AUTHOR</span> <span class="o">=</span> <span class="s2">&quot;shaenr&quot;</span>
    <span class="n">DEFAULT_STATUS</span> <span class="o">=</span> <span class="s2">&quot;published&quot;</span>  <span class="c1"># &quot;hidden&quot; and &quot;draft&quot; are other options</span>

    <span class="k">def</span> <span class="fm">__init__</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">file_path</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        pass a file_path from pelicanconf: NEWPOST_FILE</span>
<span class="sd">        pass an author from pelicanconf: MY_DEFAULT_AUTHOR</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">path</span> <span class="o">=</span> <span class="n">file_path</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">out_path</span> <span class="o">=</span> <span class="kc">None</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">raw_post_data</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_read_file_lines</span><span class="p">()</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">metadata_lines</span> <span class="o">=</span> <span class="p">[</span><span class="n">line</span> <span class="k">for</span> <span class="n">line</span> <span class="ow">in</span> <span class="bp">self</span><span class="o">.</span><span class="n">_read_metadata_lines</span><span class="p">()</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">&#39;</span><span class="se">\n</span><span class="s1">&#39;</span><span class="p">)</span> <span class="k">if</span> <span class="n">line</span><span class="p">]</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">metadata</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">build_metadata_dict</span><span class="p">()</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">content</span> <span class="o">=</span> <span class="s2">&quot;&quot;</span><span class="o">.</span><span class="n">join</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">_read_content_lines</span><span class="p">())</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">post_date</span> <span class="o">=</span> <span class="n">PostDate</span><span class="p">(</span>
            <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Date&quot;</span><span class="p">)</span>
        <span class="p">)</span>  <span class="c1"># Object for managing timestamps Use to output to self.date and self.modified</span>

        <span class="bp">self</span><span class="o">.</span><span class="n">title</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Title&quot;</span><span class="p">,</span> <span class="k">lambda</span> <span class="o">*</span><span class="n">args</span><span class="p">:</span> <span class="n">NewPost</span><span class="o">.</span><span class="n">DEFAULT_TITLE</span><span class="p">)</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">date</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">post_date</span><span class="o">.</span><span class="n">format_datetime</span><span class="p">()</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">modified</span> <span class="o">=</span> <span class="kc">None</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">tags</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Tags&quot;</span><span class="p">)</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">category</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Category&quot;</span><span class="p">)</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">slug</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Slug&quot;</span><span class="p">,</span> <span class="bp">self</span><span class="o">.</span><span class="n">get_slug_from_title</span><span class="p">)</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">_authors</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Authors&quot;</span><span class="p">)</span>  <span class="c1"># Do not use this value; use self.author</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">author</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Author&quot;</span><span class="p">,</span> <span class="k">lambda</span> <span class="o">*</span><span class="n">args</span><span class="p">:</span> <span class="n">NewPost</span><span class="o">.</span><span class="n">DEFAULT_AUTHOR</span><span class="p">)</span> \
            <span class="k">if</span> <span class="bp">self</span><span class="o">.</span><span class="n">_authors</span> <span class="ow">is</span> <span class="kc">None</span> <span class="k">else</span> <span class="bp">self</span><span class="o">.</span><span class="n">_authors</span>  <span class="c1"># force self._authors to self.author if present</span>

        <span class="bp">self</span><span class="o">.</span><span class="n">summary</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Summary&quot;</span><span class="p">)</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">lang</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Lang&quot;</span><span class="p">,</span> <span class="k">lambda</span> <span class="o">*</span><span class="n">args</span><span class="p">:</span> <span class="s2">&quot;en&quot;</span><span class="p">)</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">translation</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Translation&quot;</span><span class="p">,</span> <span class="k">lambda</span> <span class="o">*</span><span class="n">args</span><span class="p">:</span> <span class="s2">&quot;false&quot;</span><span class="p">)</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">status</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_key_else_get</span><span class="p">(</span><span class="s2">&quot;Status&quot;</span><span class="p">,</span> <span class="k">lambda</span> <span class="o">*</span><span class="n">args</span><span class="p">:</span> <span class="n">NewPost</span><span class="o">.</span><span class="n">DEFAULT_STATUS</span><span class="p">)</span>

        <span class="c1"># Not Being Used</span>
        <span class="c1"># self.template = self._get_key_else_get(&quot;Template&quot;)</span>
        <span class="c1"># self.save_as = self._get_key_else_get(&quot;Save_as&quot;)</span>
        <span class="c1"># self.url = self._get_key_else_get(&quot;Url&quot;)</span>
        <span class="c1"># self.keywords = self._get_key_else_get(&quot;Keywords&quot;)</span>

    <span class="k">def</span> <span class="fm">__str__</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="k">return</span> <span class="sa">f</span><span class="s2">&quot;NewPost: </span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">path</span><span class="si">}</span><span class="s2">&quot;</span>

    <span class="k">def</span> <span class="fm">__repr__</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="k">return</span> <span class="bp">self</span><span class="o">.</span><span class="fm">__str__</span><span class="p">()</span>

    <span class="k">def</span> <span class="nf">get_out_file_name</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="n">date</span><span class="p">,</span> <span class="n">time</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">post_date</span><span class="o">.</span><span class="n">format_datetime</span><span class="p">()</span><span class="o">.</span><span class="n">split</span><span class="p">()</span>
        <span class="n">date_time</span> <span class="o">=</span> <span class="s2">&quot;&quot;</span><span class="o">.</span><span class="n">join</span><span class="p">(</span><span class="n">date</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">&#39;-&#39;</span><span class="p">)</span> <span class="o">+</span> <span class="n">time</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">&#39;:&#39;</span><span class="p">))</span>
        <span class="k">return</span> <span class="sa">f</span><span class="s2">&quot;</span><span class="si">{</span><span class="n">date_time</span><span class="si">}</span><span class="s2">_</span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">slug</span><span class="si">}</span><span class="s2">.md&quot;</span>

    <span class="k">def</span> <span class="nf">_get_key_else_get</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">key</span><span class="p">:</span> <span class="nb">str</span><span class="p">,</span> <span class="n">else_func</span><span class="p">:</span> <span class="n">Callable</span><span class="p">[</span><span class="o">...</span><span class="p">,</span> <span class="n">Any</span><span class="p">]</span> <span class="o">=</span> <span class="k">lambda</span> <span class="o">*</span><span class="n">args</span><span class="p">:</span> <span class="kc">None</span><span class="p">)</span> <span class="o">-&gt;</span> <span class="n">Any</span><span class="p">:</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        key should be the string for a key in self.metadata</span>
<span class="sd">        If it exists, it will return it... if it doesn&#39;t, it will use else_func to return another value</span>
<span class="sd">        The default else_func will return None.</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="k">return</span> <span class="bp">self</span><span class="o">.</span><span class="n">metadata</span><span class="p">[</span><span class="n">key</span><span class="p">]</span> <span class="k">if</span> <span class="n">else_func</span> <span class="ow">is</span> <span class="ow">not</span> <span class="kc">None</span> <span class="ow">and</span> <span class="bp">self</span><span class="o">.</span><span class="n">metadata</span><span class="o">.</span><span class="fm">__contains__</span><span class="p">(</span><span class="n">key</span><span class="p">)</span> <span class="k">else</span> <span class="n">else_func</span><span class="p">(</span><span class="n">key</span><span class="p">)</span>

    <span class="k">def</span> <span class="nf">add_modified_date</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="k">return</span> <span class="bp">self</span><span class="o">.</span><span class="n">post_date</span><span class="o">.</span><span class="n">format_datetime</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">post_date</span><span class="o">.</span><span class="n">modified</span><span class="p">)</span>

    <span class="k">def</span> <span class="nf">get_slug_from_title</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">title</span><span class="p">):</span>
        <span class="k">return</span> <span class="s2">&quot;-&quot;</span><span class="o">.</span><span class="n">join</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">title</span><span class="o">.</span><span class="n">split</span><span class="p">())</span><span class="o">.</span><span class="n">lower</span><span class="p">()</span> <span class="k">if</span> <span class="bp">self</span><span class="o">.</span><span class="n">title</span> <span class="k">else</span> <span class="s2">&quot;&quot;</span>

    <span class="k">def</span> <span class="nf">_read_file_lines</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        Use self.raw_post_data</span>
<span class="sd">        Reads file in by lines. Returns raw information.</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="k">with</span> <span class="bp">self</span><span class="o">.</span><span class="n">path</span><span class="o">.</span><span class="n">open</span><span class="p">(</span><span class="s2">&quot;r&quot;</span><span class="p">)</span> <span class="k">as</span> <span class="n">read_file</span><span class="p">:</span>
            <span class="k">return</span> <span class="n">read_file</span><span class="o">.</span><span class="n">readlines</span><span class="p">()</span>

    <span class="k">def</span> <span class="nf">_read_metadata_lines</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">return_str</span><span class="o">=</span><span class="kc">None</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        Use self.metadata_lines</span>
<span class="sd">        Fines which lines are metadata lines from the total file lines.</span>
<span class="sd">        Uses the first empty space line to determine this</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="n">metadata</span> <span class="o">=</span> <span class="s2">&quot;&quot;</span>
        <span class="k">for</span> <span class="n">line</span> <span class="ow">in</span> <span class="bp">self</span><span class="o">.</span><span class="n">raw_post_data</span><span class="p">:</span>
            <span class="k">if</span> <span class="n">line</span> <span class="o">!=</span> <span class="s1">&#39;</span><span class="se">\n</span><span class="s1">&#39;</span><span class="p">:</span>
                <span class="n">metadata</span> <span class="o">+=</span> <span class="n">line</span>
            <span class="k">else</span><span class="p">:</span>
                <span class="k">break</span>
        <span class="k">return</span> <span class="n">metadata</span>

    <span class="k">def</span> <span class="nf">_read_content_lines</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="n">content</span> <span class="o">=</span> <span class="s2">&quot;&quot;</span>
        <span class="n">metadata_passed</span> <span class="o">=</span> <span class="kc">False</span>
        <span class="k">for</span> <span class="n">line</span> <span class="ow">in</span> <span class="bp">self</span><span class="o">.</span><span class="n">raw_post_data</span><span class="p">:</span>
            <span class="k">if</span> <span class="n">metadata_passed</span><span class="p">:</span>
                <span class="n">content</span> <span class="o">+=</span> <span class="n">line</span>
            <span class="k">elif</span> <span class="ow">not</span> <span class="n">metadata_passed</span> <span class="ow">and</span> <span class="ow">not</span> <span class="n">line</span> <span class="o">==</span> <span class="s1">&#39;</span><span class="se">\n</span><span class="s1">&#39;</span><span class="p">:</span>
                <span class="k">pass</span>
            <span class="k">elif</span> <span class="ow">not</span> <span class="n">metadata_passed</span> <span class="ow">and</span> <span class="n">line</span> <span class="o">==</span> <span class="s1">&#39;</span><span class="se">\n</span><span class="s1">&#39;</span><span class="p">:</span>
                <span class="n">metadata_passed</span> <span class="o">=</span> <span class="kc">True</span>
        <span class="k">return</span> <span class="n">content</span>

    <span class="k">def</span> <span class="nf">build_metadata_dict</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">add_dict</span><span class="o">=</span><span class="kc">None</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        Initialized dict and splits each line by a colon to get the key and value from the markdown metadata</span>
<span class="sd">        In case there are colons in the value, each item after the first colon is joined by a colon to a string</span>
<span class="sd">        which is used as cleaned value.</span>

<span class="sd">        You can pass dict to add_dict to add these values to the metadata</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="n">d</span> <span class="o">=</span> <span class="p">{}</span> <span class="k">if</span> <span class="n">add_dict</span> <span class="ow">is</span> <span class="kc">None</span> <span class="k">else</span> <span class="n">add_dict</span>
        <span class="k">for</span> <span class="n">line</span> <span class="ow">in</span> <span class="bp">self</span><span class="o">.</span><span class="n">metadata_lines</span><span class="p">:</span>
            <span class="n">colon_split_str</span> <span class="o">=</span> <span class="n">line</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">&#39;:&#39;</span><span class="p">)</span>
            <span class="n">k</span> <span class="o">=</span> <span class="n">colon_split_str</span><span class="p">[</span><span class="mi">0</span><span class="p">]</span><span class="o">.</span><span class="n">strip</span><span class="p">()</span>
            <span class="n">v</span> <span class="o">=</span> <span class="s2">&quot;:&quot;</span><span class="o">.</span><span class="n">join</span><span class="p">([</span>
                <span class="n">string</span><span class="o">.</span><span class="n">strip</span><span class="p">()</span> <span class="k">for</span> <span class="n">string</span> <span class="ow">in</span> <span class="n">colon_split_str</span><span class="p">[</span><span class="mi">1</span><span class="p">:]</span>
            <span class="p">])</span>
            <span class="n">d</span><span class="p">[</span><span class="n">k</span><span class="p">]</span> <span class="o">=</span> <span class="n">v</span>
        <span class="k">return</span> <span class="n">d</span>

    <span class="k">def</span> <span class="nf">create_outfile</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="k">try</span><span class="p">:</span>
            <span class="k">if</span> <span class="bp">self</span><span class="o">.</span><span class="n">out_path</span><span class="o">.</span><span class="n">exists</span><span class="p">():</span>
                <span class="nb">print</span><span class="p">(</span><span class="s2">&quot;New Post file exists&quot;</span><span class="p">)</span>
                <span class="n">exit</span><span class="p">()</span>
            <span class="k">else</span><span class="p">:</span>
                <span class="c1"># shutil.copy(self.path, self.out_path)</span>
                <span class="k">with</span> <span class="bp">self</span><span class="o">.</span><span class="n">out_path</span><span class="o">.</span><span class="n">open</span><span class="p">(</span><span class="s2">&quot;w&quot;</span><span class="p">)</span> <span class="k">as</span> <span class="n">out_file</span><span class="p">:</span>
                    <span class="n">out_file</span><span class="o">.</span><span class="n">write</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">post_date</span><span class="o">.</span><span class="n">output_meta_str</span><span class="p">)</span>
                    <span class="n">out_file</span><span class="o">.</span><span class="n">write</span><span class="p">(</span>
                        <span class="bp">self</span><span class="o">.</span><span class="n">output_metadata_to_out_file</span><span class="p">()</span>
                    <span class="p">)</span>
                    <span class="n">out_file</span><span class="o">.</span><span class="n">write</span><span class="p">(</span><span class="s2">&quot;</span><span class="se">\n</span><span class="s2">&quot;</span> <span class="o">+</span> <span class="bp">self</span><span class="o">.</span><span class="n">content</span><span class="p">)</span>
                <span class="bp">self</span><span class="o">.</span><span class="n">path</span><span class="o">.</span><span class="n">unlink</span><span class="p">()</span>
                <span class="bp">self</span><span class="o">.</span><span class="n">path</span><span class="o">.</span><span class="n">touch</span><span class="p">()</span>
        <span class="k">except</span> <span class="ne">ValueError</span><span class="p">:</span>
            <span class="k">pass</span>

    <span class="k">def</span> <span class="nf">output_metadata_to_out_file</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="n">metadata</span> <span class="o">=</span> <span class="s2">&quot;&quot;</span>
        <span class="k">for</span> <span class="n">k</span><span class="p">,</span> <span class="n">v</span> <span class="ow">in</span> <span class="bp">self</span><span class="o">.</span><span class="n">metadata</span><span class="o">.</span><span class="n">items</span><span class="p">():</span>
            <span class="n">line</span> <span class="o">=</span> <span class="sa">f</span><span class="s2">&quot;</span><span class="si">{</span><span class="n">k</span><span class="o">.</span><span class="n">capitalize</span><span class="p">()</span><span class="si">}</span><span class="s2">: </span><span class="si">{</span><span class="n">v</span><span class="si">}</span><span class="se">\n</span><span class="s2">&quot;</span>
            <span class="n">metadata</span> <span class="o">+=</span> <span class="n">line</span>
        <span class="k">return</span> <span class="n">metadata</span>


<span class="c1"># GENERATE LINKS</span>
<span class="k">class</span> <span class="nc">MenuLinks</span><span class="p">(</span><span class="nb">dict</span><span class="p">):</span>
    <span class="k">def</span> <span class="fm">__init__</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">ad</span><span class="p">):</span>
        <span class="nb">super</span><span class="p">()</span><span class="o">.</span><span class="fm">__init__</span><span class="p">(</span><span class="n">ad</span><span class="p">)</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">elems</span> <span class="o">=</span> <span class="p">[]</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">attrs</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;text&#39;</span><span class="p">,</span> <span class="s1">&#39;href&#39;</span><span class="p">,</span> <span class="s1">&#39;rel&#39;</span><span class="p">]</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">template_string</span> <span class="o">=</span> <span class="sa">r</span><span class="s1">&#39;&lt;a href=&quot;$href&quot;$rel&gt;$text&lt;/a&gt;&#39;</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">posts</span> <span class="o">=</span> <span class="n">ad</span><span class="p">[</span><span class="s1">&#39;markdown&#39;</span><span class="p">][</span><span class="s1">&#39;posts&#39;</span><span class="p">]</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">external</span> <span class="o">=</span> <span class="n">ad</span><span class="p">[</span><span class="s1">&#39;strings&#39;</span><span class="p">][</span><span class="s1">&#39;external&#39;</span><span class="p">]</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">pages</span> <span class="o">=</span> <span class="n">ad</span><span class="p">[</span><span class="s1">&#39;markdown&#39;</span><span class="p">][</span><span class="s1">&#39;pages&#39;</span><span class="p">]</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">strings</span> <span class="o">=</span> <span class="n">ad</span><span class="p">[</span><span class="s1">&#39;strings&#39;</span><span class="p">][</span><span class="s1">&#39;internal&#39;</span><span class="p">]</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">sidebar_names</span> <span class="o">=</span> <span class="p">[</span><span class="n">f_name</span><span class="o">.</span><span class="n">stem</span> <span class="k">for</span> <span class="n">f_name</span> <span class="ow">in</span> <span class="bp">self</span><span class="o">.</span><span class="n">pages</span><span class="p">]</span> <span class="o">+</span> <span class="bp">self</span><span class="o">.</span><span class="n">strings</span>

        <span class="n">num_of_rels</span> <span class="o">=</span> <span class="nb">len</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">sidebar_names</span><span class="p">)</span>

        <span class="bp">self</span><span class="o">.</span><span class="n">get_templated_elems</span><span class="p">([</span><span class="kc">None</span> <span class="k">for</span> <span class="n">rel</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="n">num_of_rels</span><span class="p">)],</span> <span class="bp">self</span><span class="o">.</span><span class="n">sidebar_names</span><span class="p">,</span> <span class="kc">None</span><span class="p">)</span>

    <span class="k">def</span> <span class="nf">print_attrs</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="k">return</span> <span class="p">[</span><span class="bp">self</span><span class="o">.</span><span class="n">elems</span><span class="p">,</span> <span class="bp">self</span><span class="o">.</span><span class="n">attrs</span><span class="p">,</span> <span class="bp">self</span><span class="o">.</span><span class="n">template_string</span><span class="p">,</span> <span class="bp">self</span><span class="o">.</span><span class="n">posts</span><span class="p">,</span>
                <span class="bp">self</span><span class="o">.</span><span class="n">external</span><span class="p">,</span> <span class="bp">self</span><span class="o">.</span><span class="n">pages</span><span class="p">,</span> <span class="bp">self</span><span class="o">.</span><span class="n">strings</span><span class="p">,</span> <span class="bp">self</span><span class="o">.</span><span class="n">sidebar_names</span><span class="p">]</span>

    <span class="k">def</span> <span class="nf">get_templated_elems</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">rel_values</span><span class="p">,</span> <span class="n">page_names</span><span class="p">,</span> <span class="n">hrefs</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;Assembly of anchor tags from markdown files and other strings via Template&quot;&quot;&quot;</span>
        <span class="n">rels</span> <span class="o">=</span> <span class="p">[</span>
            <span class="kc">None</span> <span class="k">for</span> <span class="n">_</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="nb">len</span><span class="p">(</span><span class="n">page_names</span><span class="p">))</span>
        <span class="p">]</span> <span class="k">if</span> <span class="n">rel_values</span> <span class="ow">is</span> <span class="kc">None</span> <span class="k">else</span> <span class="n">rel_values</span>

        <span class="nb">print</span><span class="p">(</span><span class="n">page_names</span><span class="p">)</span>
        <span class="nb">print</span><span class="p">(</span><span class="n">rel_values</span><span class="p">)</span>

        <span class="k">for</span> <span class="n">page</span><span class="p">,</span> <span class="n">r</span> <span class="ow">in</span> <span class="nb">zip</span><span class="p">(</span><span class="n">page_names</span><span class="p">,</span> <span class="n">rels</span><span class="p">):</span>
            <span class="n">text</span> <span class="o">=</span> <span class="n">page</span><span class="o">.</span><span class="n">title</span><span class="p">()</span>
            <span class="k">if</span> <span class="ow">not</span> <span class="n">page</span> <span class="o">==</span> <span class="s1">&#39;blog&#39;</span><span class="p">:</span>
                <span class="n">href</span> <span class="o">=</span> <span class="sa">f</span><span class="s2">&quot; </span><span class="si">{</span><span class="n">page</span><span class="si">}</span><span class="s2"> &quot;</span><span class="o">.</span><span class="n">replace</span><span class="p">(</span><span class="s1">&#39; &#39;</span><span class="p">,</span> <span class="s1">&#39;/&#39;</span><span class="p">)</span>
            <span class="k">else</span><span class="p">:</span>
                <span class="n">href</span> <span class="o">=</span> <span class="s1">&#39;/&#39;</span>
            <span class="n">rel</span> <span class="o">=</span> <span class="s1">&#39;&#39;</span> <span class="k">if</span> <span class="n">r</span> <span class="ow">is</span> <span class="kc">None</span> <span class="k">else</span> <span class="sa">f</span><span class="s1">&#39; rel=&quot;</span><span class="si">{</span><span class="n">r</span><span class="si">}</span><span class="s1">&quot;&#39;</span>

            <span class="n">temp</span> <span class="o">=</span> <span class="n">Template</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">template_string</span><span class="p">)</span>
            <span class="n">elem</span> <span class="o">=</span> <span class="n">temp</span><span class="o">.</span><span class="n">safe_substitute</span><span class="p">(</span><span class="n">text</span><span class="o">=</span><span class="n">text</span><span class="p">,</span> <span class="n">href</span><span class="o">=</span><span class="n">href</span><span class="p">,</span> <span class="n">rel</span><span class="o">=</span><span class="n">rel</span><span class="p">)</span>

            <span class="bp">self</span><span class="o">.</span><span class="n">elems</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">elem</span><span class="p">)</span>

    <span class="k">def</span> <span class="nf">sort_links</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;A simple sort, could be more interesting&quot;&quot;&quot;</span>
        <span class="n">first_items</span> <span class="o">=</span> <span class="p">[]</span>
        <span class="n">middle_items</span> <span class="o">=</span> <span class="p">[]</span>
        <span class="n">last_items</span> <span class="o">=</span> <span class="p">[]</span>
        <span class="nb">print</span><span class="p">(</span><span class="sa">f</span><span class="s2">&quot;sort links: </span><span class="si">{</span><span class="bp">self</span><span class="o">.</span><span class="n">elems</span><span class="si">}</span><span class="s2">&quot;</span><span class="p">)</span>
        <span class="k">for</span> <span class="n">elem</span> <span class="ow">in</span> <span class="bp">self</span><span class="o">.</span><span class="n">elems</span><span class="p">:</span>
            <span class="k">if</span> <span class="n">elem</span> <span class="o">==</span> <span class="s1">&#39;&lt;a href=&quot;/archive/&quot;&gt;Archive&lt;/a&gt;&#39;</span><span class="p">:</span>
                <span class="n">last_items</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">elem</span><span class="p">)</span>
            <span class="k">elif</span> <span class="n">elem</span> <span class="o">==</span> <span class="s1">&#39;&lt;a href=&quot;/blog/&quot;&gt;Home&lt;/a&gt;&#39;</span><span class="p">:</span>
                <span class="n">first_items</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">elem</span><span class="p">)</span>
            <span class="k">else</span><span class="p">:</span>
                <span class="n">middle_items</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="n">elem</span><span class="p">)</span>

        <span class="n">concat_items</span> <span class="o">=</span> <span class="n">first_items</span> <span class="o">+</span> <span class="nb">sorted</span><span class="p">(</span><span class="n">middle_items</span><span class="p">)</span> <span class="o">+</span> <span class="n">last_items</span>
        <span class="k">assert</span> <span class="nb">len</span><span class="p">(</span><span class="n">concat_items</span><span class="p">)</span> <span class="o">==</span> <span class="nb">len</span><span class="p">(</span><span class="bp">self</span><span class="o">.</span><span class="n">elems</span><span class="p">)</span>

        <span class="bp">self</span><span class="o">.</span><span class="n">elems</span> <span class="o">=</span> <span class="n">concat_items</span>
        <span class="k">return</span> <span class="bp">self</span><span class="o">.</span><span class="n">elems</span>


<span class="c1"># UTILS</span>
<span class="k">def</span> <span class="nf">get_files_by_pattern</span><span class="p">(</span><span class="n">dir_path</span><span class="p">:</span> <span class="n">Path</span><span class="p">,</span> <span class="n">pattern</span><span class="p">:</span> <span class="nb">str</span> <span class="o">=</span> <span class="s2">&quot;*.md&quot;</span><span class="p">)</span> <span class="o">-&gt;</span> <span class="nb">list</span><span class="p">:</span>
    <span class="k">return</span> <span class="p">[</span><span class="n">f</span> <span class="k">for</span> <span class="n">f</span> <span class="ow">in</span> <span class="n">dir_path</span><span class="o">.</span><span class="n">iterdir</span><span class="p">()</span>
            <span class="k">if</span> <span class="n">f</span><span class="o">.</span><span class="n">glob</span><span class="p">(</span><span class="n">pattern</span><span class="p">)</span>
            <span class="ow">and</span> <span class="ow">not</span> <span class="n">f</span><span class="o">.</span><span class="n">is_dir</span><span class="p">()]</span>


<span class="k">def</span> <span class="nf">get_new_post_file_paths_by_prefix</span><span class="p">():</span>
    <span class="n">glob_str</span> <span class="o">=</span> <span class="sa">f</span><span class="s1">&#39;</span><span class="si">{</span><span class="n">NEWPOST_INFILE_PREFIX</span><span class="si">}</span><span class="s1">*&#39;</span>
    <span class="k">return</span> <span class="p">[</span><span class="n">new_post_md</span> <span class="k">for</span> <span class="n">new_post_md</span>
            <span class="ow">in</span> <span class="n">PROJECT_DIR</span><span class="o">.</span><span class="n">glob</span><span class="p">(</span><span class="n">glob_str</span><span class="p">)]</span>


<span class="k">def</span> <span class="nf">check_posts</span><span class="p">():</span>
    <span class="n">posts</span> <span class="o">=</span> <span class="p">[</span><span class="n">NewPost</span><span class="p">(</span><span class="n">post_path</span><span class="p">)</span> <span class="k">for</span> <span class="n">post_path</span> <span class="ow">in</span> <span class="n">get_new_post_file_paths_by_prefix</span><span class="p">()]</span>
    <span class="k">for</span> <span class="n">p</span> <span class="ow">in</span> <span class="n">posts</span><span class="p">:</span>
        <span class="n">fname</span> <span class="o">=</span> <span class="n">p</span><span class="o">.</span><span class="n">get_out_file_name</span><span class="p">()</span>
        <span class="n">p</span><span class="o">.</span><span class="n">out_path</span> <span class="o">=</span> <span class="n">CONTENT_DIR</span> <span class="o">/</span> <span class="n">fname</span>
        <span class="n">p</span><span class="o">.</span><span class="n">create_outfile</span><span class="p">()</span>


<span class="c1"># CUSTOM FILTERS</span>
<span class="nd">@environmentfilter</span>
<span class="k">def</span> <span class="nf">divide_iter</span><span class="p">(</span><span class="n">environment</span><span class="p">:</span> <span class="n">Environment</span><span class="p">,</span> <span class="n">inputs</span><span class="p">:</span> <span class="nb">list</span><span class="p">,</span> <span class="n">n</span><span class="p">:</span> <span class="nb">int</span><span class="p">)</span> <span class="o">-&gt;</span> <span class="nb">zip</span><span class="p">:</span>
    <span class="sd">&quot;&quot;&quot;Divides Items into Paired Tuples&quot;&quot;&quot;</span>
    <span class="n">iters</span> <span class="o">=</span> <span class="p">[</span><span class="nb">iter</span><span class="p">(</span><span class="n">inputs</span><span class="p">)]</span> <span class="o">*</span> <span class="n">n</span>
    <span class="k">return</span> <span class="nb">zip</span><span class="p">(</span><span class="o">*</span><span class="n">iters</span><span class="p">)</span>


<span class="c1"># Theme-specific settings</span>
<span class="n">SITENAME</span> <span class="o">=</span> <span class="s1">&#39;shaenr&#39;</span>
<span class="n">DOMAIN</span> <span class="o">=</span> <span class="s1">&#39;shaenr.github.io&#39;</span>
<span class="n">BIO_TEXT</span> <span class="o">=</span> <span class="s1">&#39;Python/JavaScript&lt;br&gt;Command Line Cowboy&lt;br&gt;San Antonio, TX&#39;</span>
<span class="n">FOOTER_TEXT</span> <span class="o">=</span> <span class="s1">&#39;This site is statically generated with Python.&#39;</span>

<span class="n">SITE_AUTHOR</span> <span class="o">=</span> <span class="s1">&#39;shaenr&#39;</span>
<span class="c1"># TWITTER_USERNAME = &#39;&#39;</span>
<span class="n">INDEX_DESCRIPTION</span> <span class="o">=</span> <span class="s1">&#39;Latest posts by python developer shaenr&#39;</span>

<span class="n">ICONS_PATH</span> <span class="o">=</span> <span class="s1">&#39;images/icons&#39;</span>
<span class="n">GITHUB_URL</span> <span class="o">=</span> <span class="s1">&#39;http://github.com/shaenr/&#39;</span>
<span class="n">SOCIAL_ICONS</span> <span class="o">=</span> <span class="p">[</span>
    <span class="p">(</span><span class="s1">&#39;/atom.xml&#39;</span><span class="p">,</span> <span class="s1">&#39;Atom Feed&#39;</span><span class="p">,</span> <span class="s1">&#39;fa-feed&#39;</span><span class="p">),</span>
    <span class="p">(</span><span class="s1">&#39;https://github.com/shaenr&#39;</span><span class="p">,</span> <span class="s1">&#39;GitHub&#39;</span><span class="p">,</span> <span class="s1">&#39;fa-github&#39;</span><span class="p">),</span>
    <span class="p">(</span><span class="s1">&#39;https://hackers.town/@shaen&#39;</span><span class="p">,</span> <span class="s1">&#39;Mastodon&#39;</span><span class="p">,</span> <span class="s1">&#39;fa-mastodon&#39;</span><span class="p">),</span>
    <span class="p">(</span><span class="s1">&#39;https://divsel.neocities.org&#39;</span><span class="p">,</span> <span class="s1">&#39;Neocities&#39;</span><span class="p">,</span> <span class="s1">&#39;fa-share&#39;</span><span class="p">),</span>
<span class="p">]</span>

<span class="c1">#THEME_COLOR = &#39;#FF8000&#39;</span>

<span class="c1"># Pelican settings</span>
<span class="n">RELATIVE_URLS</span> <span class="o">=</span> <span class="kc">True</span>
<span class="n">SITEURL</span> <span class="o">=</span> <span class="sa">f</span><span class="s1">&#39;https://</span><span class="si">{</span><span class="n">DOMAIN</span><span class="si">}</span><span class="s1">&#39;</span>
<span class="n">TIMEZONE</span> <span class="o">=</span> <span class="s1">&#39;America/Chicago&#39;</span>
<span class="n">DEFAULT_DATE</span> <span class="o">=</span> <span class="s1">&#39;fs&#39;</span>
<span class="n">DEFAULT_DATE_FORMAT</span> <span class="o">=</span> <span class="s1">&#39;%B </span><span class="si">%d</span><span class="s1">, %Y&#39;</span>
<span class="n">DEFAULT_PAGINATION</span> <span class="o">=</span> <span class="kc">True</span>
<span class="n">SUMMARY_MAX_LENGTH</span> <span class="o">=</span> <span class="mi">42</span>

<span class="c1"># ARTICLE SAVES</span>
<span class="n">ARTICLE_URL</span> <span class="o">=</span> <span class="s1">&#39;{date:%Y}/{date:%m}/</span><span class="si">{slug}</span><span class="s1">/&#39;</span>
<span class="n">ARTICLE_SAVE_AS</span> <span class="o">=</span> <span class="n">ARTICLE_URL</span> <span class="o">+</span> <span class="s1">&#39;index.html&#39;</span>

<span class="c1"># PAGE SAVES</span>
<span class="n">PAGE_URL</span> <span class="o">=</span> <span class="s1">&#39;</span><span class="si">{slug}</span><span class="s1">/&#39;</span>

<span class="c1"># ARCHIVE SAVES</span>
<span class="n">ARCHIVES_SAVE_AS</span> <span class="o">=</span> <span class="s1">&#39;archive/index.html&#39;</span>
<span class="n">YEAR_ARCHIVE_SAVE_AS</span> <span class="o">=</span> <span class="s1">&#39;{date:%Y}/index.html&#39;</span>
<span class="n">MONTH_ARCHIVE_SAVE_AS</span> <span class="o">=</span> <span class="s1">&#39;{date:%Y}/{date:%m}/index.html&#39;</span>

<span class="c1"># Disable authors, categories, tags, and category pages</span>
<span class="c1"># DIRECT_TEMPLATES = [&#39;index&#39;, &#39;archives&#39;]</span>
<span class="n">DIRECT_TEMPLATES</span> <span class="o">=</span> <span class="p">(</span><span class="s1">&#39;index&#39;</span><span class="p">,</span> <span class="s1">&#39;tags&#39;</span><span class="p">,</span> <span class="s1">&#39;categories&#39;</span><span class="p">,</span> <span class="s1">&#39;archives&#39;</span><span class="p">)</span>

<span class="c1"># Disable Atom feed generation</span>
<span class="n">FEED_ATOM</span> <span class="o">=</span> <span class="s1">&#39;atom.xml&#39;</span>
<span class="n">FEED_ALL_ATOM</span> <span class="o">=</span> <span class="kc">None</span>
<span class="n">CATEGORY_FEED_ATOM</span> <span class="o">=</span> <span class="kc">None</span>
<span class="n">TRANSLATION_FEED_ATOM</span> <span class="o">=</span> <span class="kc">None</span>

<span class="n">TYPOGRIFY</span> <span class="o">=</span> <span class="kc">True</span>

<span class="n">MARKDOWN</span> <span class="o">=</span> <span class="p">{</span>
    <span class="s1">&#39;extension_configs&#39;</span><span class="p">:</span> <span class="p">{</span>
        <span class="s1">&#39;markdown.extensions.admonition&#39;</span><span class="p">:</span> <span class="p">{},</span>
        <span class="s1">&#39;markdown.extensions.codehilite&#39;</span><span class="p">:</span> <span class="p">{</span><span class="s1">&#39;linenums&#39;</span><span class="p">:</span> <span class="kc">None</span><span class="p">},</span>
        <span class="s1">&#39;markdown.extensions.extra&#39;</span><span class="p">:</span> <span class="p">{},</span>
    <span class="p">},</span>
    <span class="s1">&#39;output_format&#39;</span><span class="p">:</span> <span class="s1">&#39;html5&#39;</span><span class="p">,</span>
<span class="p">}</span>

<span class="n">CACHE_CONTENT</span> <span class="o">=</span> <span class="kc">False</span>
<span class="n">DELETE_OUTPUT_DIRECTORY</span> <span class="o">=</span> <span class="kc">True</span>

<span class="c1"># TEMPLATES</span>
<span class="n">templates</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;404.html&#39;</span><span class="p">]</span>
<span class="n">TEMPLATE_PAGES</span> <span class="o">=</span> <span class="p">{</span><span class="n">page</span><span class="p">:</span> <span class="n">page</span> <span class="k">for</span> <span class="n">page</span> <span class="ow">in</span> <span class="n">templates</span><span class="p">}</span>

<span class="n">extras</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;CNAME&#39;</span><span class="p">,</span> <span class="s1">&#39;favicon.ico&#39;</span><span class="p">,</span> <span class="s1">&#39;robots.txt&#39;</span><span class="p">]</span>
<span class="n">EXTRA_PATH_METADATA</span> <span class="o">=</span> <span class="p">{</span><span class="s1">&#39;extra/</span><span class="si">%s</span><span class="s1">&#39;</span> <span class="o">%</span> <span class="n">file</span><span class="p">:</span> <span class="p">{</span><span class="s1">&#39;path&#39;</span><span class="p">:</span> <span class="n">file</span><span class="p">}</span> <span class="k">for</span> <span class="n">file</span> <span class="ow">in</span> <span class="n">extras</span><span class="p">}</span>  <span class="c1"># understand this line</span>

<span class="c1"># PLUGINS</span>
<span class="n">PLUGIN_PATHS</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;plugins&#39;</span><span class="p">]</span>
<span class="n">PLUGINS</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;assets&#39;</span><span class="p">,</span> <span class="s1">&#39;neighbors&#39;</span><span class="p">,</span> <span class="s1">&#39;render_math&#39;</span><span class="p">]</span>

<span class="c1"># ASSETS PLUGIN</span>
<span class="n">ASSET_SOURCE_PATHS</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;static&#39;</span><span class="p">]</span>
<span class="n">ASSET_CONFIG</span> <span class="o">=</span> <span class="p">[</span>
    <span class="p">(</span><span class="s1">&#39;cache&#39;</span><span class="p">,</span> <span class="kc">False</span><span class="p">),</span>
    <span class="p">(</span><span class="s1">&#39;manifest&#39;</span><span class="p">,</span> <span class="kc">False</span><span class="p">),</span>
    <span class="p">(</span><span class="s1">&#39;url_expire&#39;</span><span class="p">,</span> <span class="kc">False</span><span class="p">),</span>
    <span class="p">(</span><span class="s1">&#39;versions&#39;</span><span class="p">,</span> <span class="kc">False</span><span class="p">),</span>
<span class="p">]</span>

<span class="c1"># EXCLUDES</span>
<span class="n">PAGE_EXCLUDES</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;other&#39;</span><span class="p">,</span> <span class="s1">&#39;images&#39;</span><span class="p">]</span>
<span class="n">ARTICLE_EXCLUDES</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;other&#39;</span><span class="p">,</span> <span class="s1">&#39;images&#39;</span><span class="p">]</span>

<span class="n">JINJA_FILTERS</span> <span class="o">=</span> <span class="p">{</span><span class="s1">&#39;divide_iter&#39;</span><span class="p">:</span> <span class="n">divide_iter</span><span class="p">}</span>

<span class="n">GOOGLE_ANALYTICS</span> <span class="o">=</span> <span class="kc">False</span>

<span class="c1"># PELICAN SETTINGS PATHS</span>
<span class="n">OUTPUT_PATH</span> <span class="o">=</span> <span class="s1">&#39;output&#39;</span>
<span class="n">PATH</span> <span class="o">=</span> <span class="s1">&#39;content&#39;</span>
<span class="n">THEME</span> <span class="o">=</span> <span class="s1">&#39;theme&#39;</span>
<span class="n">STATIC_PATHS</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;images&#39;</span><span class="p">,</span> <span class="s1">&#39;files&#39;</span><span class="p">,</span> <span class="s1">&#39;extra&#39;</span><span class="p">]</span>
<span class="n">IGNORE_FILES</span> <span class="o">=</span> <span class="p">[</span><span class="s1">&#39;.DS_Store&#39;</span><span class="p">,</span> <span class="s1">&#39;pneumatic.scss&#39;</span><span class="p">,</span> <span class="s1">&#39;pygments.css&#39;</span><span class="p">,</span> <span class="s1">&#39;old_content&#39;</span><span class="p">,</span> <span class="s1">&#39;other&#39;</span><span class="p">]</span>

<span class="c1"># PATHLIB PATHS</span>
<span class="n">PROJECT_DIR</span> <span class="o">=</span> <span class="n">Path</span><span class="p">(</span><span class="s2">&quot;.&quot;</span><span class="p">)</span>
<span class="n">CONTENT_DIR</span> <span class="o">=</span> <span class="n">PROJECT_DIR</span> <span class="o">/</span> <span class="n">PATH</span>
<span class="n">PAGES_DIR</span> <span class="o">=</span> <span class="n">CONTENT_DIR</span> <span class="o">/</span> <span class="s1">&#39;pages&#39;</span>

<span class="n">NEWPOST_INFILE_PREFIX</span> <span class="o">=</span> <span class="n">PROJECT_DIR</span> <span class="o">/</span> <span class="s2">&quot;newpost_&quot;</span>

<span class="n">EXTERNAL_LINKS</span> <span class="o">=</span> <span class="p">[</span>
    <span class="p">(</span><span class="s1">&#39;ProtonMail&#39;</span><span class="p">,</span> <span class="s1">&#39;https://www.protonmail.com&#39;</span><span class="p">,</span> <span class="s1">&#39;external&#39;</span><span class="p">),</span>
    <span class="p">(</span><span class="s1">&#39;hackers.town&#39;</span><span class="p">,</span> <span class="s1">&#39;https://www.hackers.town&#39;</span><span class="p">,</span> <span class="s1">&#39;external&#39;</span><span class="p">)</span>
<span class="p">]</span>

<span class="c1"># AUTOMATE ADDING SIDEBAR LINKS</span>
<span class="n">links</span> <span class="o">=</span> <span class="n">MenuLinks</span><span class="p">(</span>
    <span class="p">{</span><span class="s1">&#39;markdown&#39;</span><span class="p">:</span> <span class="p">{</span>
        <span class="s1">&#39;posts&#39;</span><span class="p">:</span> <span class="n">get_files_by_pattern</span><span class="p">(</span><span class="n">CONTENT_DIR</span><span class="p">),</span>
        <span class="s1">&#39;pages&#39;</span><span class="p">:</span> <span class="n">get_files_by_pattern</span><span class="p">(</span><span class="n">PAGES_DIR</span><span class="p">)},</span>
        <span class="s1">&#39;strings&#39;</span><span class="p">:</span> <span class="p">{</span>
            <span class="s1">&#39;internal&#39;</span><span class="p">:</span> <span class="p">[</span><span class="s1">&#39;archive&#39;</span><span class="p">],</span>
            <span class="s1">&#39;external&#39;</span><span class="p">:</span> <span class="n">EXTERNAL_LINKS</span><span class="p">}})</span>

<span class="n">links</span><span class="o">.</span><span class="n">elems</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="s1">&#39;&lt;a href=&quot;/&quot;&gt;Home&lt;/a&gt;&#39;</span><span class="p">)</span>
<span class="n">links</span><span class="o">.</span><span class="n">sidebar_names</span><span class="o">.</span><span class="n">append</span><span class="p">(</span><span class="s1">&#39;blog&#39;</span><span class="p">)</span>

<span class="k">assert</span> <span class="nb">len</span><span class="p">(</span><span class="n">links</span><span class="o">.</span><span class="n">elems</span><span class="p">)</span> <span class="o">%</span> <span class="mi">2</span> <span class="o">==</span> <span class="mi">0</span>

<span class="n">SIDEBAR_LINKS</span> <span class="o">=</span> <span class="n">links</span><span class="o">.</span><span class="n">sort_links</span><span class="p">()</span>

<span class="n">MARKDOWN</span> <span class="o">=</span> <span class="p">{</span>
    <span class="s1">&#39;extension_configs&#39;</span><span class="p">:</span> <span class="p">{</span>
        <span class="s1">&#39;markdown.extensions.admonition&#39;</span><span class="p">:</span> <span class="p">{},</span>
        <span class="s1">&#39;markdown.extensions.codehilite&#39;</span><span class="p">:</span> <span class="p">{</span><span class="s1">&#39;linenums&#39;</span><span class="p">:</span> <span class="kc">None</span><span class="p">},</span>
        <span class="s1">&#39;markdown.extensions.extra&#39;</span><span class="p">:</span> <span class="p">{},</span>
    <span class="p">},</span>
    <span class="s1">&#39;output_format&#39;</span><span class="p">:</span> <span class="s1">&#39;html5&#39;</span><span class="p">,</span>
<span class="p">}</span>

<span class="n">DISPLAY_CATEGORIES_ON_MENU</span> <span class="o">=</span> <span class="kc">True</span>
<span class="n">DISPLAY_PAGES_ON_MENU</span> <span class="o">=</span> <span class="kc">False</span>

<span class="n">MENUITEMS</span> <span class="o">=</span> <span class="p">[</span>
    <span class="p">(</span><span class="s2">&quot;Test&quot;</span><span class="p">,</span> <span class="s2">&quot;https://www.duckduckgo.com&quot;</span><span class="p">)</span>
<span class="p">]</span>
</code></pre></div>


<h2>What Does This God-forsaken File Do&nbsp;Now?</h2>
<p>In addition to several implemented features that just don&#8217;t work due to not being supported by the theme and not made to do so yet, these are things that are actually&nbsp;working:</p>
<p>It formats the appearance of my menu links based on if there are an odd or even number of pages. Right now there is just &#8220;Home and Archive so it is formatted as &#8220;Home | Archive &#8230; if there were more pages to link, it would pair them in doubles like that, leaving the last one in the middle by itself if the total number is&nbsp;odd.</p>
<p>It handles writing a new post from a specific template file where it will add all of the appropriate metadata to the <span class="caps">MD</span> file that gets built. It&#8217;s possible &#8216;all&#8217; is not the right word here. It gets the date, the slug, the author, and some basic things I want everything to&nbsp;have.</p>
<h2>The Bad&nbsp;Parts</h2>
<p>I think it is possible that some of the things I&#8217;ve written are probably redundancy of actions that are sometimes already easier to do within default Pelican once you learn how it works and what the options there&nbsp;are.</p>
<p>Some of the stuff I don&#8217;t even really want to use anymore (a lot of clutter where I was figuring out how Pelican would work and trying different things), but the meat of it &#8212; the part that allows me to write a post without thinking about what to name the file or how to get the timestamp into the filename so that it persists longer than the system metadata &#8212; is a small part that I think is a great idea that is working&nbsp;well.</p>
<p>I definitely have to cut out the stuff that I don&#8217;t need anymore because it&#8217;s not being used. And when code is that messy due to existing for multiple generation of tinkering with it in different ways and not cleaning it up, its not&nbsp;good.</p>
<h2>One File to Rule&nbsp;Them</h2>
<p>The hilarious thing is, even though there are three classes written to accomplish these things, all this and several utility functions are all just smashed into the <code>pelicanconf.py</code> file, because&#8230; when I tried to break it down into modules, it just was not working due to various errors including import hell and whatever&nbsp;else&#8230; </p>
<p>I could just be stupid for not seeing the problem&#8230;, But things were just not working until I put everything back into the one file, so I did that to reduce the number of problems I was having at the&nbsp;moment.</p>
<p>It&#8217;s possible that I just need to open a new branch, try to do the refactor again, and hopefully this time I will not err in the way that made things not&nbsp;work.</p>
<div class="codehilite"><pre><span></span><code><span class="k">One</span> <span class="k">File</span> <span class="k">to</span> <span class="n">Bind</span> <span class="n">Them</span><span class="p">.</span> 
<span class="k">One</span> <span class="k">file</span> <span class="k">to</span> <span class="n">define</span> <span class="k">every</span> <span class="n">class</span> 
<span class="k">function</span> <span class="k">and</span> <span class="k">value</span><span class="p">,</span> 
<span class="k">and</span> <span class="k">in</span> <span class="n">the</span> <span class="n">darkness</span> <span class="n">instantiate</span><span class="p">,</span> 
<span class="k">call</span><span class="p">,</span> <span class="k">and</span> <span class="k">reference</span> <span class="n">them</span> <span class="n">entirely</span> <span class="k">from</span> <span class="n n-Quoted">`__main__`</span><span class="p">.</span>
</code></pre></div>