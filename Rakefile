FILE_NAME = "book-example"

BOOK_DIR = "book"
OUT_DIR = "out"
SOURCE_LANGUAGE_CODE = :en

LANGUAGES = [
  :en,
  :pt_br
]

FILE_EXTENSIONS = [
  :pdf,
  :epub,
]

PANDOC_DEFAULT_LATEX_TEMPLATE = '~/.pandoc/templates/eisvogel/eisvogel.latex'
PANDOC_MAIN_CALL_ARGS = %{
  cd #{BOOK_DIR} && \
    pandoc \
      --template #{PANDOC_DEFAULT_LATEX_TEMPLATE} \
      --filter pandoc-include \
      --standalone \
      --table-of-contents
}

CONTENT_FILES = [
  'metadata.yml',
]

namespace :book do
  def sanitize(cmd)
    cmd.gsub(/\s+/, " ").gsub(/\\/, "").strip
  end

  def rake_workaround!(task)
    ARGV.each {|a| task a.to_sym do ; end }
  end

  def chapter_exists?(chapter_file_path)
    Dir[chapter_file_path].count == 1
  end

  def get_argv_values()
    file_extension = ARGV[1].to_s.downcase
    language = ARGV[2].to_s.downcase.gsub(/-/, '_')
    return file_extension, language
  end

  def get_content_files_args(language)
    main_content_files = CONTENT_FILES
    chapter_files = Dir["#{BOOK_DIR}/#{language}/**.md"].collect {|c| c.gsub(/#{BOOK_DIR}\//, '') }

    main_content_files.concat(chapter_files).join(' ')
  end

  def validate!()
    raise 'No arguments found' if ARGV.empty?

    file_extension, language = get_argv_values()

    raise 'Invalid file extension' unless FILE_EXTENSIONS.include?(file_extension.to_sym)
    raise 'Invalid language' unless LANGUAGES.include?(language.to_sym)
  end

  def get_output_path(language, file_name, file_extension)
    "#{OUT_DIR}/#{language}/#{file_name}.#{file_extension}"
  end

  def translate_to_gramma(language)
    idiom, country = language.to_s.split('_')
    language.to_s.gsub(/_/, '-').gsub(country, country.upcase)
  end

  task :create_out_dir do
    folders = LANGUAGES.collect{|l| "#{BOOK_DIR}/#{OUT_DIR}/#{l}"}.join(" ")

    sh "mkdir -p #{folders}"
  end

  desc 'Build Book given format and language'
  task :build => [:create_out_dir] do |task|
    validate!()
    rake_workaround!(task)

    file_extension, language = get_argv_values()
    output_path = get_output_path(language, FILE_NAME, file_extension)
		content_files_args = get_content_files_args(language)

    output_args = %{
      --to #{file_extension} \
      --output #{output_path} \
    }

    cmd = %{
      #{PANDOC_MAIN_CALL_ARGS} \
        #{output_args} \
          #{content_files_args}
    }

    sh sanitize(cmd)
  end

  desc 'Open Book given format and language'
  task :look do |task|
    validate!()
    rake_workaround!(task)

    file_extension, language = get_argv_values()
    file_path = "#{BOOK_DIR}/#{OUT_DIR}/#{language}/#{FILE_NAME}.#{file_extension}"

    sh "open #{file_path}"
  end

  desc 'Translate Book given an language'
  task :translate do |task|
    raise 'No arguments found' if ARGV.empty?
    raise 'Invalid arguments passed' if ARGV.size > 2

    language = ARGV[1].to_s.downcase.gsub(/-/, '_').to_sym
    raise 'Unsupported language' unless LANGUAGES.include?(language)

    rake_workaround!(task)

    source_folder_path = "#{BOOK_DIR}/#{SOURCE_LANGUAGE_CODE}"
    destination_folder_path = "#{BOOK_DIR}/#{language}"

    sh "rm -rf #{destination_folder_path}"
    sh "mkdir -p #{destination_folder_path}"

    markdown_files_path = "#{source_folder_path}/*.md"
    markdowns = Dir[markdown_files_path]
    trans_language_code = /_|-/.match?(language) ? language.to_s.upcase.gsub(/_/, '-') : language

    markdowns.each do |src_md_file|
      file_name = File.basename(src_md_file)
      translate_command_call = %{
        trans #{SOURCE_LANGUAGE_CODE}:#{trans_language_code} \
          --brief \
          --input #{source_folder_path}/#{file_name} \
          --output #{destination_folder_path}/#{file_name}
      }

      sh sanitize(translate_command_call)
    end
  end

  desc 'Check Book grammar given a language chapter'
  task :grammar do |task|
    raise 'No arguments found' if ARGV.empty?
    raise 'Invalid arguments passed' if ARGV.size > 3

    language = ARGV[1].to_s.downcase.gsub(/-/, '_').to_sym
    raise 'Unsupported language' unless LANGUAGES.include?(language)

    rake_workaround!(task)

    chapter = ARGV[2].to_s
    chapter_file_path = "#{BOOK_DIR}/#{language}/#{chapter}*.md"
    raise 'No chapter found' unless chapter_exists?(chapter_file_path)

    grammar_check_command = "gramma check --language #{translate_to_gramma(language)} --markdown #{chapter_file_path}"
    sh grammar_check_command
  end
end
