require "stringex"

root_dir = `pwd`
post_dir = "#{root_dir[0..-2]}/_posts"

desc "Begin a new post in _posts"
task :post, :title do |t, args|
  args.with_defaults(:title => 'new-post')
  title = args.title
  filename = "#{post_dir}/#{Time.new.strftime('%Y-%m-%d')}-#{title.to_url}.md"

  if File.exist?(filename)
    system "open #{filename}"
    abort("#{filename} already exists.\nOpening to edit.\nrake aborted!")
  end

  puts "Creating new post: #{filename}"

  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
    post.puts "comments: true"
    post.puts "categories: \n"
    post.puts "---"
  end
  system "open #{filename}"
end
