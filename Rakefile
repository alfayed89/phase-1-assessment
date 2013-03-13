require 'rake/clean'

CLEAN = FileList['../phase-1-assessment.zip', 'README.pdf', 'top.html', 'top.pdf', 'problem-??.html', 'problem-??.pdf']

task :checkprogs do
  ["multimarkdown", "wkhtmltopdf", "gs"].each do |prog|
    raise "You need to install '#{prog}'. Try homebrew!" unless system "#{prog} --help > /dev/null 2>&1"
  end
end


task :docs => [:checkprogs] do
  puts "Creating top-level PDF ..."
  success = system "multimarkdown -o top.html README.md"
  raise "ERROR: multimarkdown command failed on README.md" unless success
  success = system "wkhtmltopdf -q top.html top.pdf"
  raise "ERROR: wkhtmltopdf command failed" unless success

  puts "Creating problem PDFs ..."
  problem_dirs = Dir.glob("problem-??")
  problem_dirs.each do |dir|
    success = system "multimarkdown -o #{dir}.html #{dir}/README.md"
    raise "ERROR: multimarkdown command failed on #{dir}/README.md" unless success

    success = system "wkhtmltopdf -q #{dir}.html #{dir}.pdf"
    raise "ERROR: wkhtmltopdf command failed" unless success
  end

  puts "Merging PDFs ..."
  pdf_files = problem_dirs.map { |d| "#{d}.pdf" }
  success = system "gs -q -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -sOutputFile=README.pdf top.pdf #{pdf_files.join(' ')}"
  raise "ERROR: gs command failed" unless success
end


task :archive do
  unless File.exist? "README.pdf"
    Rake::Task[:docs].invoke
  end

  success = system "git archive HEAD --format=zip > ../phase-1-assessment.zip"
  raise "ERROR: unable to create zipfile from git repo" unless success

  success = system "zip -q ../phase-1-assessment.zip README.pdf"
  raise "ERROR: unable to add README.pdf to zipfile" unless success
end
