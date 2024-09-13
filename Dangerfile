# Dangerfile

# swiftlint
github.dismiss_out_of_range_messages
swiftlint.config_file = '.swiftlint.yml'
swiftlint.binary_path = './Pods/SwiftLint/swiftlint'
swiftlint.lint_files(inline_mode: true)


# preserves_vector_represantation
require "json"

github.dismiss_out_of_range_messages

# xcassets 内に追加された PDF ファイルに絞る
pdf_files = git.added_files
  .select { |added_file| added_file.end_with?(".pdf") && File.dirname(added_file).include?(".imageset") }

pdf_files.each do |pdf_file|
  contents_json = "#{File.dirname(pdf_file)}/Contents.json"
  data = JSON.load_file(contents_json, symbolize_names: true)

  # properties OR preserves-vector-representation がないのでダメ
  if data[:properties].nil? || data[:properties][:"preserves-vector-representation"].nil? || !data[:properties][:"preserves-vector-representation"]
    line = File.read(contents_json).count("\n")
    send(:warn, "`Preserve Vector Data` にチェックをいれてください。", file: contents_json, line: line)
  end
end


# iblinter
github.dismiss_out_of_range_messages
iblinter.binary_path = "./Pods/IBLinter/bin/iblinter"
iblinter.lint


# color_asset_eight_bit_hexadecimal
require "json"

github.dismiss_out_of_range_messages

# ファイル置き換えに対応する
# Ref: https://github.com/andre-alves/danger-missing_codeowners/blob/5bdfb921ead20fa971590f3ea11f8c91adebefa7/lib/missing_codeowners/plugin.rb#L89-L94
renamed_files_hash = git.renamed_files.map { |rename| [rename[:before], rename[:after]] }.to_h
post_rename_modified_files = git.modified_files.map { |modified_file| renamed_files_hash[modified_file] || modified_file }
files = (post_rename_modified_files - git.deleted_files) + git.added_files

# xcassets 内に追加されたカラーアセットに絞る
color_json_files = files
  .select { |f| f.end_with?(".json") && File.dirname(f).include?(".colorset") }

color_json_files.each do |color_json_file|
  filelines = File.readlines(color_json_file)
  filelines.each_with_index do |line, index|
    next unless line.strip().start_with?("\"blue\" : \"")
    unless line.include?("\"0x")
      send(:warn, "8-bit Hexadecimalで設定してください。", file: color_json_file, line: index + 3)
    end
  end
end


# background_linter
github.dismiss_out_of_range_messages

# ファイル置き換えに対応する
# Ref: https://github.com/andre-alves/danger-missing_codeowners/blob/5bdfb921ead20fa971590f3ea11f8c91adebefa7/lib/missing_codeowners/plugin.rb#L89-L94
renamed_files_hash = git.renamed_files.map { |rename| [rename[:before], rename[:after]] }.to_h
post_rename_modified_files = git.modified_files.map { |modified_file| renamed_files_hash[modified_file] || modified_file }
files = (post_rename_modified_files - git.deleted_files) + git.added_files

files.each do |modified|
  next unless modified.end_with?(".xib", ".storyboard")
  filelines = File.readlines(modified)
  filelines.each_with_index do |line, index|
    if line.include?("systemColor name")
      send(:warn, "システムカラーを使用せずにプロジェクトで定義した色を使用してください。", file: modified, line: index + 2)
    end
  end
end