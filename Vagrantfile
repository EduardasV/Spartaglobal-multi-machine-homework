# Install required plugins
required_plugins = ["vagrant-hostsupdater"]
required_plugins.each do |plugin|
    exec "vagrant plugin install #{plugin}" unless Vagrant.has_plugin? plugin
end

DB_ips = {
  "DB_HOST" => "192.168.10.101"
}
def add_bash_code(obj)
  app_box = <<~HEREDOC
  HEREDOC
  obj.each do |key, val|
    app_box += <<~HEREDOC
      sudo sed -i "1iexport #{key}=#{val}" ~/.bashrc
    HEREDOC
  end
  app_box += <<~HEREDOC
    source ~/.bashrc
  HEREDOC
end

Vagrant.configure("2") do |config|

  config.vm.define "app" do |app|
    app.vm.box = "ubuntu/xenial64"
    app.vm.network("private_network", ip: "192.168.10.100")
    app.hostsupdater.aliases = ["development.local"]
    app.vm.synced_folder("app", "/home/ubuntu/app")
    app.vm.provision("shell", path: "environment/app/provision.sh", privileged: false)
    app.vm.provision("shell", inline: add_bash_code(DB_ips), privileged: false)
  end

  DB_ips.each do |key, val|
    config.vm.define "#{key}" do |db|
      db.vm.box = "ubuntu/xenial64"
      db.vm.network("private_network", ip: "#{val}")
      db.hostsupdater.aliases = ["development.local.db.#{key}"]
      db.vm.synced_folder("environment/db", "/home/ubuntu/environment")
      db.vm.provision("shell", path: "environment/db/provision.sh", privileged: false)
    end
  end
end
