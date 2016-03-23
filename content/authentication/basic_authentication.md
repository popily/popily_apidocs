Title: Basic Authentication
Created: 2016-03-01 12:00
Modified: 2016-03-22 12:00
Category: Authentication
Tags:
Slug: basic_authentication
Authors: Popily
Summary: Basic Authentication In The Popily API

You authenticate each request an API key. Soon you&rsquo;ll get an API get as soon as you create your account. For now though, you need to email us at awesome@popily.com to as for one.

## API key
<pre class="highlight python"><code><span class="kn">from</span> <span class="nn">popily_api</span> <span class="kn">import</span> <span class="n">Popily</span>
<span class="n">popily</span> <span class="o">=</span> <span class="n">Popily</span><span class="p">(</span><span class="s">'YOUR API KEY'</span><span class="p">)</span>
</code></pre>
<pre class="highlight javascript"><code><span class="c1">// Node.js</span>
<span class="kd">var</span> <span class="nx">popily</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="s1">'popily'</span><span class="p">)(</span><span class="s1">'YOUR API TOKEN'</span><span class="p">);</span>

<span class="c1">// With the browser client</span>
<span class="kd">var</span> <span class="nx">popily</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">Popily</span><span class="p">(</span><span class="s1">'YOUR API KEY'</span><span class="p">);</span>
</code></pre>

<p>You authenticate with Popily using a token passed in the Authorization HTTP header. If you haven&rsquo;t been assigned a token, just contact us and we&rsquo;ll hook you up. <strong>We strongly recommend keeping this token to yourself.</strong></p>
