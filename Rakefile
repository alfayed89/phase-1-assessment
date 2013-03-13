require 'rake/clean'

CLEAN = FileList['README.pdf', 'top.html', 'top.pdf', 'problem-??.html', 'problem-??.pdf']

task :checkprogs do
  ["multimarkdown", "wkhtmltopdf", "gs"].each do |prog|
    tmpfile = "/tmp/#{prog}-#{Time.now.to_i}"
    raise "You need to install '#{prog}'. Try homebrew!" unless system "#{prog} --help > #{tmpfile} 2>&1"
  end
end


task :docs => [:checkprogs] do
  puts "Creating top-level PDF ..."
  success = system "multimarkdown -o top.html README.md"
  raise "ERROR: multimarkdown command failed on README.md" if not success
  success = system "wkhtmltopdf -q top.html top.pdf"
  raise "ERROR: wkhtmltopdf command failed" if not success

  puts "Creating problem PDFs ..."
  problem_dirs = Dir.glob("problem-??")
  problem_dirs.each do |dir|
    success = system "multimarkdown -o #{dir}.html #{dir}/README.md"
    raise "ERROR: multimarkdown command failed on #{dir}/README.md" if not success

    success = system "wkhtmltopdf -q #{dir}.html #{dir}.pdf"
    raise "ERROR: wkhtmltopdf command failed" if not success
  end

  puts "Merging PDFs ..."
  pdf_files = problem_dirs.map { |d| "#{d}.pdf" }
  success = system "gs -q -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -sOutputFile=README.pdf top.pdf #{pdf_files.join(' ')}"
  raise "ERROR: gs command failed" if not success
end


task :archive