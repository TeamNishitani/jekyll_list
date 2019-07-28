やったこと
----------

-   blog2jekyllを改良し，画像(svgも)を表示できるように
-   以下の手順で(とりあえずベタ書き)
    -   mdに変換後,リンクがあれば抽出
    -   バックスラッシュが付いてしまうので，バックスラッシュが無いように書き換え,mdを更新

コード
------

\#+begin~examples~ \#!/usr/bin/env ruby

require 'org-ruby' require 'fileutils' require 'colorize' def
split~headbody~(conts) body = conts m = conts.scan(/---\n(.\*?)---\n/m)
if m.size&gt;0 head = "---\n" m.each do |match| head &lt;&lt; match\[0\]
conts.gsub!("---\n"~~match\[0\]~~"---\n", '') end head &lt;&lt; "---\n"
else head = '' end return head, body end

def conv~mdandstore~(file~name~, conts) head, conts =
split~headbody~(conts) File.write("./tmp.org",conts) \# need revise to
use temporary class file = File.join(\$jekyll~postspath~, file~name~)
File.write("tmp.md", head) command = "pandoc -f org -t markdown
./tmp.org &gt;&gt; tmp.md" system command FileUtils.cp('tmp.md', file)
FileUtils.rm('tmp.md') FileUtils.rm('tmp.org')

end

def rm~backslashmd~ Dir.glob("\#{\$jekyll~postspath~}/\*.md") do |file|
md~cont~ = '' file~name~ =
File.join(\$jekyll~postspath~,File.basename(file))
File.foreach(file~name~) do |line| img = line.match *!\
$$a\$$${{site.baseurl}}\/assets\/images\/(.*)$* if img != nil line
&lt;&lt; "!\[a\]({{site.baseurl}}/assets/images/\#{img\[1\]})" md~cont~
![a]({{site.baseurl}}/assets/images/\#{img\[1\]})&lt;&lt; line else md~cont~ &lt;&lt; line end end
rev~md~(file~name~,md~cont~) end end

def rev~md~(file~name~,md~cont~) File.write(file~name~,md~cont~) end

\$jekyll~postspath~ = '/Users/hiroto/jekyll~ornb~/jekyll~list~/~posts~'
\$myhelp~imagespath~ = File.join ENV\['HOME'\],'.my~help~/images/'
\$show~img~ = "!\[a\]\({{site.baseurl}}/assets/images/" \$assets~img~ =
'*Users/hiroto/jekyll~ornb~/jekyll~list~/assets*' \$jekyll~imgpath~ =
File.join ENV\['HOME'\],'/jekyll~ornb~/jekyll~list~/~posts~' cont = ''
file~name~ = '' cont~start~ = false cont~img~ = false

FileUtils.cp~r~(\$myhelp~imagespath~, \$assets~img~)

\['blog.org'\].each do |blog~file~| file = File.join ENV\['HOME'\],
'.my~help~', blog~file~ File.readlines(file).each do |line| m1 =
line.match *\^\* (.+) \<(.+) .+ * m2 = line.match *\^\* (.+)
\<(\[\d|-\]\*).\*(\d{2}:\d{2}\*) * img = line.match
*$$\[file:(.*)\/(.*)$$\]*

if img != nil print "img\[2\] &gt;&gt;&gt;&gt;&gt;&gt;&gt;\#{img\[2\]}"
img~path~ = \$myhelp~imagespath~ + img\[2\] puts 'copy~imagename~ -&gt;
'.yellow + img\[2\].green

cont &lt;&lt; "\#{\$show~img~}\#{img\[2\]})" cont~img~ = true end if (m1
or m2) m = m2 || m1 conv~mdandstore~(file~name~, cont) if cont~start~ ==
true cont = m2 ? "---\ndate: \#{m2\[2\]} \#{m2\[3\]}\n---\n" : ""
cont~start~ = true p file~name~ = "\#{m\[2\]}-\#{m\[1\].split('
').join('-')}.md" line = '' end if cont~img~ == false cont &lt;&lt; line
if cont~start~ == true end conv~mdandstore~(file~name~, cont) cont~img~
= false end end

rm~backslashmd~

\#+end~example~

-   test

!\[a\]({{site.baseurl}}/assets/images/nichiyama-blog-red.svg)
![a]({{site.baseurl}}/assets/images/nichiyama-blog-red.svg)