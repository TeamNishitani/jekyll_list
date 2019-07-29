やったこと
----------

-   blog2jekyllを改良し，画像(svgも)を表示できるように
-   以下の手順で(とりあえずベタ書き)
    -   mdに変換後,リンクがあれば抽出
    -   バックスラッシュが付いてしまうので，バックスラッシュが無いように書き換え,mdを更新

コード
------

> ``` {.example}
> #!/usr/bin/env ruby
> # coding: utf-8
>
> require 'org-ruby'
> require 'fileutils'
> require 'colorize'
> def split_head_body(conts)
>   body = conts
>   m = conts.scan(/---\n(.*?)---\n/m)
>   if m.size>0
>     head = "---\n"
>     m.each do |match|
>       head << match[0]
>       conts.gsub!("---\n"+match[0]+"---\n", '')
>     end
>     head << "---\n"
>   else
>     head = ''
>   end
>   return head, body
> end
>
> def conv_md_and_store(file_name, conts)
>   head, conts = split_head_body(conts)
>   File.write("./tmp.org",conts)  # need revise to use temporary class
>   file = File.join($jekyll_posts_path, file_name)
>   File.write("tmp.md", head)
>   command = "pandoc -f org -t markdown ./tmp.org >> tmp.md"
>   system command
>   FileUtils.cp('tmp.md', file)
>   FileUtils.rm('tmp.md')
>   FileUtils.rm('tmp.org')
>
> end
>
>
> def rm_backslash_md
>   Dir.glob("#{$jekyll_posts_path}/*.md") do |file|
>     md_cont = ''
>     file_name = File.join($jekyll_posts_path,File.basename(file))
>     File.foreach(file_name) do |line|
>       img = line.match /!\\\[a\\]\({{site.baseurl}}\/assets\/images\/(.*)\)/
>       if img != nil
>         line = "![a]({{site.baseurl}}/assets/images/#{img[1]})"
>         md_cont << line
>       else
>         md_cont << line
>       end
>     end
>     rev_md(file_name,md_cont)
>   end
> end
>
> def rev_md(file_name,md_cont)
>   File.write(file_name,md_cont)
> end
>
>
> $jekyll_posts_path = '/Users/hiroto/jekyll_ornb/jekyll_list/_posts'
> $myhelp_images_path = File.join ENV['HOME'],'.my_help/images/'
> $show_img = "![a]\({{site.baseurl}}/assets/images/"
> $assets_img = '/Users/hiroto/jekyll_ornb/jekyll_list/assets/'
> $jekyll_img_path = File.join ENV['HOME'],'/jekyll_ornb/jekyll_list/_posts'
> cont = ''
> file_name = ''
> cont_start = false
> cont_img = false
>
> FileUtils.cp_r($myhelp_images_path, $assets_img)
>
> ['blog.org'].each do |blog_file|
>   file = File.join ENV['HOME'], '.my_help', blog_file
>   File.readlines(file).each do |line|
>     m1 = line.match /^\* (.+) \<(.+) .+\>/
>     m2 = line.match /\^* (.+) \<([\d|-]*).*(\d{2}:\d{2}*)\>/
>     img = line.match /\[\[file:(.*)\/(.*)\]\]/
>
>     if img != nil
>       print  "img[2] >>>>>>>#{img[2]}"
>       img_path = $myhelp_images_path + img[2]
>       puts 'copy_image_name -> '.yellow + img[2].green
> #      FileUtils.cp(img_path, $jekyll_img_path)
>       cont << "#{$show_img}#{img[2]})"
>       cont_img = true      
>     end
>     if (m1 or m2)
>       m = m2 || m1
>       conv_md_and_store(file_name, cont) if cont_start == true
>       cont = m2 ? "---\ndate: #{m2[2]} #{m2[3]}\n---\n" : ""
>       cont_start = true
>       p file_name = "#{m[2]}-#{m[1].split(' ').join('-')}.md"
>       line = ''
>     end
>     if cont_img == false
>       cont << line if cont_start == true
>     end
>     conv_md_and_store(file_name, cont)
>     cont_img = false
>   end
> end
>
> rm_backslash_md  
> ```

-   test

![a]({{site.baseurl}}/assets/images/nichiyama-blog-red.svg)