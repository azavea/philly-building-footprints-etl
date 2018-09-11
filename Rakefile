task default: %[build]

desc "Run psql"
task :psql do
  puts "Running psql ->"
  sh "docker-compose exec database gosu postgres psql -d buildings"
end

desc "Build"
task :build => [
    :check_env,
    :start_database,
    :download_buildings,
    :install_postgis,
    :import_buildings,
    :create_philadelphia_table
]

desc "Verifying environment"
task :check_env do
  puts "Verifying environment"
  if !(system "which curl")
    abort "Missing required dependency curl"
  end

  if !(system "which ogr2ogr")
    abort "Missing required dependency ogr2ogr"
  end

  if !(Dir.exists?("./data"))
    puts "Creating ./data directory"
    Dir.mkdir("./data")
  end
end

desc "Download Pennsylvania building footprint data"
task :download_buildings do
  puts "Downloading Pennsylvania building footprint data ->"
  sh "curl -o ./data/Pennsylvania.zip https://usbuildingdata.blob.core.windows.net/usbuildings-v1-1/Pennsylvania.zip"
  puts "Unzipping Pennsylvania building footprint data ->"
  Dir.chdir("data") do
    sh "unzip ./Pennsylvania.zip"
  end
end

desc "Remove downloaded data"
task :clean do
  puts "Removing Pennsylvania geojson data ->"
  rm "./data/Pennsylvania.geojson" if File.exists? "./data/Pennsylvania.geojson"
  rm "./data/Pennsylvania.zip" if File.exists? "./data/Pennsylvania.zip"
end

desc "Start database"
task :start_database do
  puts "Starting database ->"
  sh "docker-compose up -d"
end

desc "Install PostGIS"
task :install_postgis do
  puts "Installing PostGIS extension ->"
  sh "docker-compose exec database gosu postgres psql -d buildings -c 'CREATE EXTENSION postgis;'"
end

desc "Import Pennsylvania buildings into PostGIS"
task :import_buildings do
  puts "Importing Pennsylvania building data into PostGIS ->"
  sh "ogr2ogr -progress -s_srs \"EPSG:4326\" -t_srs \"EPSG:3857\" -f \"PostgreSQL\" PG:\"host=localhost dbname=buildings user=phillybuildings password=phillybuildings\" \"./data/Pennsylvania.geojson\" -nln pennsylvania"
end

desc "Create Philadelphia buildings table ->"
task :create_philadelphia_table do
  puts "Creating Philadelphia buildings table ->"
  sh "docker-compose exec database gosu postgres psql -d buildings --file=/usr/src/sql/create_philadelphia_table.sql"
end
