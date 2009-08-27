require 'hpricot'

docs_dir   = File.dirname(__FILE__) + '/docs'
images_dir = File.dirname(__FILE__) + '/images'
styles_dir = File.dirname(__FILE__) + '/styles'
output_dir = File.dirname(__FILE__) + '/output'
bin_dir    = File.dirname(__FILE__) + '/bin'
skin       = File.dirname(__FILE__) + '/skin/skin.html.tmpl'
markdown   = bin_dir + '/Markdown.pl'

def create_toc(html)
  toc = %Q(<ul class="toc">\n)
  doc = Hpricot( html )
  (doc/'h2').each do |h2|
    h2_anchor = make_anchor( h2.inner_html )
    toc += %Q(  <li><a href="##{h2_anchor}">#{h2.inner_html}</a>\n)
    h2.inner_html = %Q(<a name="#{h2_anchor}">#{h2.inner_html}</a>) 
    cur = h2
    first = true
    while ( cur = cur.next_sibling )
      break if ( cur.name == 'h2' )
      if ( cur.name == 'h3' )
        if ( first )
          first = false 
          toc += %Q(    <ul>\n)
        end
        h3_anchor = "#{h2_anchor}_#{make_anchor( cur.inner_html )}"
        toc += %Q(  <li><a href="##{h3_anchor}">#{cur.inner_html}</a>\n)
        cur.inner_html = %Q(<a name="#{h3_anchor}">#{cur.inner_html}</a>) 
      end
    end
    if ( ! first )
      toc += %Q(    </ul>\n)
    end
    toc += %Q(  </li>\n)
  end
  toc += %Q(</ul>\n)
  return [ toc, doc.to_s ]
end

def make_anchor(text)
  anchor = text.downcase.gsub( /\s/, '_' )
  puts "in #{text} out #{anchor}"
  anchor
end

task :default do
  FileUtils.mkdir_p( output_dir )
  Dir.chdir( docs_dir ) do
    Dir[ '*.mdown' ].each do |doc|
      html = `#{markdown} #{doc}`
      toc, html = create_toc( html )
      output = File.read( skin ) 
      output.sub!( /\$REPLACE\$/, toc + html )
      File.open( "#{output_dir}/#{File.basename(doc, '.mdown')}.html", 'w' ) {|f| f << output }
    end
  end
  FileUtils.cp_r( images_dir, output_dir )
  FileUtils.cp_r( styles_dir, output_dir )
end

task :clean do
  FileUtils.rm_rf( output_dir )
end

