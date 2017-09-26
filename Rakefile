require 'httparty'
require 'selenium-webdriver'
require 'pry'
require 'rake'

# COPY_CMD = case ENV.fetch('OS')
#              when 'osx'   then 'pbcopy'
#              when 'linux' then 'xclip'
#            end
# PASTE_KEYS = case ENV.fetch('OS')
#                when 'osx'   then [:shift, :insert]
#                when 'linux' then [:control, 'v']
#              end
URL = case ENV.fetch('SITE')
        when 'beta' then 'https://beta.swarmsim.com'
        when 'prod' then 'https://swarmsim.github.io'
      end
FOLDER = ENV.fetch('SITE')
OPTIONS = ENV.fetch('HEADLESS') == 'true' ? Selenium::WebDriver::Remote::Capabilities.chrome(chromeOptions: {args: ["--headless", "--disable-gpu"]}) : Selenium::WebDriver::Remote::Capabilities.chrome
DRIVER = Selenium::WebDriver.for :chrome, desired_capabilities: OPTIONS

desc 'using this to test docker'
task :docker do
  puts 'Hello from Docker!'
end

desc 'do stuff while waiting for more energy'
task :wait do
  count = 0
  driver = DRIVER
  init
  import_swarm
  open_tab

  while true do
    # meat
    driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
    grow_all_the_meat_without_warp

    # larvae
    if !(driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a/span[2]'}).empty?) then
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
      multiply_all_the_larvae
    end

    # # territory
    # driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[3]/a'}).click
    # claim_all_the_territory

    # wait
    count = count + 1
    puts count
    sleep(60)
  end
end

desc 'do all the things'
task :swarmbot do
  crystal_count = 1
  driver = DRIVER
  init
  import_swarm
  open_tab
  # binding.pry
  while true do
    # current ascension cost
    driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[5]/a'}).click
    ascension_cost = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/unitdesc/p/p[4]'}).text.split(' ')[4].delete(',').to_f

    # current energy units
    driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[4]/a'}).click
    current_energy = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/unitdesc/p/p[2]'}).text.split(' ').first.delete(',').to_f
    if ((current_energy > 120000) || (current_energy > (ascension_cost + 6800))) then
      puts "Growing some meat"

      driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[2]/div/buyupgrade/span/div/*'}).first.click
      sleep(1.5)
      
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
      do_all_the_things

    else
      # get crystals
      puts "Claiming Crystals (#{crystal_count} batches claimed so far)"
      crystal_count = crystal_count + 1
      get_all_the_crystals_and_wait(1800)
    end
  end
end

desc 'do all the things better'
task :better_swarmbot do
  driver = DRIVER
  init
  import_swarm
  open_tab
  # binding.pry
  
  while true do
    units_and_costs = update_units_and_costs
    ascension_cost = units_and_costs[:ascension_cost]
    current_bats = units_and_costs[:current_bats]
    current_lepidoptera = units_and_costs[:current_lepidoptera]
    current_nightbugs = units_and_costs[:current_nightbugs]
    current_crystals = units_and_costs[:current_crystals]
    current_nexuses = units_and_costs[:current_nexuses]
    current_energy = units_and_costs[:current_energy]

    if (current_lepidoptera < 9000) then
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[2]'}).click
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/form/input'}).send_keys('@9k')
      driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.text != "Can't buy"
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[6]/td[2]'}).click
      wait_count = do_all_the_things_without_warp
      if (wait_count >= 30) then
        puts "Claiming Crystals while waiting for butterflies (#{current_crystals} currently owned)"
        get_all_the_crystals_and_wait(60)
      else
        sleep(60)
      end
    elsif (current_bats < 1000) then
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[2]'}).click
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/form/input'}).send_keys('@1k')
      driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.text != "Can't buy"
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[6]/td[2]'}).click
      wait_count = do_all_the_things_without_warp
      if (wait_count >= 30) then
        puts "Claiming Crystals while waiting for bats (#{current_crystals} currently owned)"
        get_all_the_crystals_and_wait(60)
      else
        sleep(60)
      end
    elsif (current_nightbugs <= 7) then
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[6]/td[2]'}).click
      while (driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[1]/div/buyupgrade/span/div/*'}).first.text != "Can't cast" && current_nightbugs <= 7) do
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[1]/div/buyupgrade/span/div/*'}).first.click
        increment_nightbugs
        current_nightbugs = current_nightbugs + 1
      end
      do_all_the_things_without_warp_or_twins
      puts "Claiming Crystals while rushing larvae (#{current_crystals} currently owned)"
      get_all_the_crystals_and_wait(1800)
    elsif (current_nightbugs == 8) then
      cocoon_larvae
      if (driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.text != "Can't cast") then
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.click
        increment_nightbugs
      end
      do_all_the_things_without_warp_or_twins
      puts "Claiming Crystals while building cocoons (#{current_crystals} currently owned)"
      get_all_the_crystals_and_wait(1800)
    elsif (current_nightbugs == 9) then
      cocoon_larvae
      if (driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.text != "Can't cast") then
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.click
        increment_nightbugs
        max_nightbugs = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/upgradedesc/p/p[2]/span/a'}).text.split(' ').first
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[2]'}).click
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/form/input'}).send_keys("@#{max_nightbugs}")
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.click
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]'}).click
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[4]/a'}).click
      end
      do_all_the_things_without_warp_or_twins
      puts "Claiming crystals while building cocoons (#{current_crystals} currently owned)"
      get_all_the_crystals_and_wait(1800)
    elsif (current_nightbugs == 10) then
      # num_nightbugs = 10     : clone, buy territory upgrades, repeat
      no_more_upgrades = false
      while (!no_more_upgrades) do
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[4]/a'}).click
        if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.text != "Can't cast" then
          driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.click
          driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[3]/a'}).click
          no_more_upgrades = twin_territory_upgrades
        else
          do_all_the_things_without_warp_or_twins
          puts "Claiming crystals while twinning territory units (#{current_crystals} currently owned)"
          get_all_the_crystals_and_wait(1800)
        end
      end
      increment_nightbugs
    elsif (current_nightbugs == 11) then
      # num_nightbugs = 11     : clone, buy all overmind V upgrades
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
      driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[1]"}).click
      driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).last.click
      driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[1]"}).click
      driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy"
      driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[1]"}).click
      driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li/div[1]/buyupgrade/span/div/*'}).last.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy"
      increment_nightbugs
    elsif (current_nightbugs == 12) then
      # num_nightbugs = 12     : clone, buy any remaining overmind v upgrades
      if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.text != "Can't cast" then
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.click
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
        driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[1]"}).click
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).last.click
        driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[1]"}).click
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy"
        driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[1]"}).click
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li/div[1]/buyupgrade/span/div/*'}).last.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy"
        increment_nightbugs
      else
        do_all_the_things_without_warp_or_twins
        puts "Claiming crystals while upgrading Overmind V (#{current_crystals} currently owned)"
        get_all_the_crystals_and_wait(1800)
      end

    elsif (current_nightbugs >= 13 && current_nightbugs <= 16)  then
      # num_nightbugs = 13 - 16: clone larvae
      if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.text != "Can't cast" then
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.click
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
        driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[1]"}).click
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/buyunit-input/div/buyunit/span/div/*'}).last.click
        increment_nightbugs
      else
        do_all_the_things_without_warp_or_twins
        puts "Claiming crystals while cloning larvae (#{current_crystals} currently owned)"
        get_all_the_crystals_and_wait(1800)
      end
    elsif (current_nightbugs == 17) then
      # num_nightbugs = 17     : clone, uncocoon, buy overmind v
      if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.text != "Can't cast" then
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[5]/div/buyupgrade/span/div/*'}).first.click
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).last.click
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
        driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[1]"}).click
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/buyunit-input/div/buyunit/span/div/*'}).last.click
        increment_nightbugs
      else
        do_all_the_things_without_warp_or_twins
        puts "Claiming crystals while buying Overmind V (#{current_crystals} currently owned)"
        get_all_the_crystals_and_wait(1800)
      end
    elsif ((current_energy > 120000) || (current_energy > (ascension_cost + 6800))) then
      while ((current_energy > 120000) || (current_energy > (ascension_cost + 6800))) do
        puts "Growing some meat"
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[6]/td[2]'}).click
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[2]/div/buyupgrade/span/div/*'}).first.click
        sleep(1.5)
        driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
        do_all_the_things
        units_and_costs = update_units_and_costs
        ascension_cost = units_and_costs[:ascension_cost]
        current_crystals = units_and_costs[:current_crystals]
        current_energy = units_and_costs[:current_energy]
      end
      puts "Claiming Crystals (#{current_crystals} currently owned)"
      get_all_the_crystals_and_wait(1800)
    else
      do_all_the_things_without_warp_or_counting
      # get crystals
      puts "Claiming Crystals (#{current_crystals} currently owned)"
      get_all_the_crystals_and_wait(1800)
    end
  end
end

# Mapping
#
# num_nightbugs = 0 - 7  : get larvae by using larvae rush
# num_nightbugs = 8      : get larvae using clone larvae
# num_nightbugs = 9      : get larvae using clone larvae, and set number of cocoons
# num_nightbugs = 10     : clone, buy territory upgrades, repeat
# num_nightbugs = 11     : clone, buy all overmind V upgrades
# num_nightbugs = 12     : clone, buy any remaining overmind v upgrades
# num_nightbugs = 13 - 16: clone larvae
# num_nightbugs = 17     : clone, uncocoon, buy overmind v

desc 'do all the investment for the future'
task :investing_swarmbot do
  driver = DRIVER
  init
  import_swarm
  open_tab
  # binding.pry
  
  while true do
    flag = false

    units_and_costs = update_units_and_costs
    current_energy = units_and_costs[:current_energy]
    current_crystals = units_and_costs[:current_crystals]
    current_lepidoptera = units_and_costs[:current_lepidoptera]
    
    if (current_lepidoptera < 39000) then
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[2]'}).click
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/form/input'}).send_keys('@39k')
      driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.text != "Can't buy"
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[6]/td[2]'}).click
      puts "Claiming Crystals while waiting for butterflies (#{current_crystals} currently owned)"
      get_all_the_crystals_for_the_future_and_wait(1800, false)
    elsif (current_energy > 7300) then
      while (current_energy > 7300) do
        puts "Cloning my army MUAHAHAHAHA"
        driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[6]/div/buyupgrade/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/ul/li[6]/div/buyupgrade/span/div/*'}).last.text != "Can't cast"
        flag = flag || do_all_the_things_for_the_future

        units_and_costs = update_units_and_costs
        current_energy = units_and_costs[:current_energy]
        current_crystals = units_and_costs[:current_crystals]
      end
      puts "Claiming Crystals (#{current_crystals} currently owned)"
      get_all_the_crystals_for_the_future_and_wait(1800, flag)
    else
      flag = flag || do_all_the_things_for_the_future
      # get crystals
      puts "Claiming Crystals (#{current_crystals} currently owned)"
      get_all_the_crystals_for_the_future_and_wait(1800, flag)
    end
  end
end

desc 'Hit \'em quick, get out fast. Chicka-Pow!'
task :tap do
  driver = DRIVER
  init
  import_swarm
  open_tab
  binding.pry
  export_swarm
end

private

def update_units_and_costs
  driver = DRIVER

  # current ascension cost
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[5]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[12]/td[2]'}).click
  ascension_cost = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/unitdesc/p/p[4]'}).text.split(' ')[4].delete(',').to_f

  # current energy units
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[4]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[6]/td[2]'}).click
  current_bats = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[2]/a/div[2]'}).text.delete(',').to_i
  current_lepidoptera = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[2]/a/div[2]'}).text.delete(',').to_i
  current_nightbugs = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]/a/div[2]'}).text.delete(',').to_i
  current_crystals = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[4]/td[2]/a/div[2]'}).text.delete(',').to_f
  current_nexuses = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[5]/td[2]/a/div[2]'}).text.delete(',').to_i
  current_energy = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[6]/td[2]/a/div[2]'}).text.split(' ').first.delete(',').to_f
  {ascension_cost: ascension_cost, current_bats: current_bats, current_lepidoptera: current_lepidoptera, current_nightbugs: current_nightbugs, current_crystals: current_crystals, current_nexuses: current_nexuses, current_energy: current_energy}
end

def init
  # startup
  driver = DRIVER
  driver.navigate.to URL

  # get save file
  old_save = HTTParty.post("https://content.dropboxapi.com/2/files/download", headers: {'Authorization' => "Bearer ARClIUaWD-8AAAAAAAATnoDdImiaS7e2hg4fnsThvfDhnKHfZ4FOlUJn-g-DSCvo", "Dropbox-API-Arg" => "{\"path\": \"/#{FOLDER}/swarm.sav\"}", "Content-Type" => ""})

  # import save
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[3]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[3]/ul/li[5]/a'}).click
  driver.find_element({xpath: '//*[@id="export"]'}).clear
  # IO.popen(COPY_CMD, 'w') { |k| k << old_save }
  # driver.find_element({xpath: '//*[@id="export"]'}).send_keys(PASTE_KEYS)
  driver.execute_script("document.getElementById('export').value=arguments[0]", old_save)
  driver.find_element({id: 'export'}).send_keys([' ', :backspace])
end

def open_tab
  driver = DRIVER

  # meat
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/table/tbody/tr[1]/td[2]'}).click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[2]'}).empty?
  
  # larvae
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/table/tbody/tr[3]/td[2]'}).click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]'}).empty?
  
  # territory
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[3]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/table/tbody/tr[1]/td[2]'}).click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[2]'}).empty?
  
  # energy
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[4]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/table/tbody/tr[6]/td[2]'}).click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[6]/td[2]'}).empty?
  
  # mutagen
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[5]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/table/tbody/tr[12]/td[2]'}).click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[12]/td[2]'}).empty?
end

def grow_all_the_meat
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[2]'}).click
  driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/buyunit-input/div/buyunit/span/div/*'}).last.click
  (1..20).each do |i|
    while !(driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[1]/span"}).empty?) do
      driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[2]"}).click
      bank_man, bank_exp = driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[2]/a/div[2]"}).text.split('e')
      cost_man, cost_exp = driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[1]/div[1]/p/cost/span/span[1]"}).text.split(' ').first.split('e')
      warp_man, warp_exp = driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/span"}).text.split(' ').first.split('e')

      if ((bank_man.to_f * (10 ** (bank_exp.to_i - cost_exp.to_i))) + (warp_man.to_f * (10 ** (warp_exp.to_i - cost_exp.to_i)))) >= (cost_man.to_f * 2)
        driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[1]/div[1]/buyupgrade/span/div/*"}).first.click if driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[1]/div[1]/buyupgrade/span/div/a[1]"}).first.text != "Can't buy"
      else
        break
      end
    end
  end
end

def grow_all_the_meat_without_warp
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click
  (1..20).each do |i|
    while !(driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[1]/span"}).empty?) do
      driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[2]"}).click
      bank_man, bank_exp = driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[2]/a/div[2]"}).text.split('e')
      cost_man, cost_exp = driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[1]/div[1]/p/cost/span/span[1]"}).text.split(' ').first.split('e')
      
      if ((bank_man.to_f * (10 ** (bank_exp.to_i - cost_exp.to_i))) >= (cost_man.to_f * 2))
        driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[1]/div[1]/buyupgrade/span/div/*"}).first.click if driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[1]/div[1]/buyupgrade/span/div/a[1]"}).first.text != "Can't buy"
      else
        break
      end
    end
  end
end

def grow_all_the_meat_for_the_future
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[1]/a'}).click

  # buy overmind 6
  driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[1]"}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/form/input'}).send_keys("50%")
  # binding.pry
  driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).first.click
  
  # buy overmind 5
  driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[1]"}).click
  driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy"
  driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/buyunit-input/div/buyunit/span/div/*'}).last.click

  (1..20).each do |i|
    while !(driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[1]/span"}).empty?) do
      driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[2]"}).click
      bank_man, bank_exp = driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[2]/a/div[2]"}).text.split('e')
      cost_man, cost_exp = driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[1]/div[1]/p/cost/span/span[1]"}).text.split(' ').first.split('e')
      
      if ((bank_man.to_f * (10 ** (bank_exp.to_i - cost_exp.to_i))) >= (cost_man.to_f * 2))
        driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[1]/div[1]/buyupgrade/span/div/*"}).first.click if driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[6]/ul/li[1]/div[1]/buyupgrade/span/div/a[1]"}).first.text != "Can't buy"
      else
        break
      end
    end
  end
end

def multiply_all_the_larvae
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]'}).click
  driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*'}).last.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy"
  # driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy"
end

def multiply_all_the_larvae_for_the_future
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]'}).click
  # driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*'}).last.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy"
  if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy" then
    driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).last.click
    true
  else
    false
  end
end

def claim_all_the_territory
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[3]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[2]'}).click
  driver.find_elements(xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*').last.click if driver.find_elements(xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*').last.text != "Can't buy"
  twin_man, twin_exp = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/form/span[2]'}).text.split(' ').first[2..-1].split('e')
  purchase_man, purchase_exp = to_standard_form((twin_man.to_f * 512), twin_exp.to_i)
  comparison_man, comparison_exp = to_standard_form((twin_man.to_f * 511), twin_exp.to_i)
  owned_man, owned_exp = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/p/ng-pluralize'}).text.split(' ')[2] == "no" ? [0, 0] : driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/p/ng-pluralize'}).text.split(' ')[2].split('e')
  if ((comparison_exp.to_i > owned_exp.to_i) || ((comparison_exp.to_i == owned_exp.to_i) && (comparison_man.to_f > owned_man.to_f))) then
    driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/form/input'}).send_keys("@#{purchase_man}e#{purchase_exp}")
    driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).first.text != "Can't hatch"
  end
  while !(driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[11]/td[1]/span"}).empty?) do
    driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[11]/td[2]'}).click
    driver.find_elements(xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*').last.click if driver.find_elements(xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*').last.text != "Can't buy"
    driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).first.text != "Can't buy"
    driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/form/input'}).send_keys("@#{purchase_man}e#{purchase_exp}")
    driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).first.text != "Can't hatch"
  end
end

def claim_all_the_territory_without_twins
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[3]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[2]'}).click
  twin_man, twin_exp = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/form/span[2]'}).text.split(' ').first[2..-1].split('e')
  purchase_man, purchase_exp = to_standard_form((twin_man.to_f * 512), twin_exp.to_i)
  comparison_man, comparison_exp = to_standard_form((twin_man.to_f * 511), twin_exp.to_i)
  owned_man, owned_exp = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/p/ng-pluralize'}).text.split(' ')[2] == "no" ? [0, 0] : driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/p/ng-pluralize'}).text.split(' ')[2].split('e')
  if ((comparison_exp.to_i > owned_exp.to_i) || ((comparison_exp.to_i == owned_exp.to_i) && (comparison_man.to_f > owned_man.to_f))) then
    driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/form/input'}).send_keys("@#{purchase_man}e#{purchase_exp}")
    driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).first.text != "Can't hatch"
  end
  while !(driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[11]/td[1]/span"}).empty?) do
    driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[11]/td[2]'}).click
    if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).first.text != "Can't buy" then
      driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).first.click
      driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/form/input'}).send_keys("@#{purchase_man}e#{purchase_exp}")
      driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).first.text != "Can't hatch"
    else
      break
    end
  end
end

def get_all_the_crystals
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]'}).click
  driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[2]/div[1]/buyupgrade/span/div/*'}).first.text != "Can't buy"
end

def get_all_the_crystals_for_the_future
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]'}).click
  driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*'}).first.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*'}).last.text != "Can't buy"
end

def get_all_the_crystals_and_wait(time)
  driver = DRIVER
  get_all_the_crystals
  export_swarm
  sleep(time)
  import_swarm
  open_tab
end

def get_all_the_crystals_for_the_future_and_wait(time, flag)
  driver = DRIVER
  get_all_the_crystals_for_the_future if !flag
  export_swarm
  sleep(time)
  import_swarm
  open_tab
end

def twin_territory_upgrades
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[3]/a'}).click
  no_more_upgrades_to_buy = true
  (1..11).each do |i|
    if !(driver.find_elements({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[1]/span"}).empty?) then
      driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[#{i}]/td[2]"}).click
      if driver.find_elements(xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*').last.text != "Can't buy" then
        driver.find_elements(xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[5]/ul/li[1]/div[1]/buyupgrade/span/div/*').last.click 
        no_more_upgrades_to_buy = false
      end
    end
  end
  driver.find_element({xpath: "/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[1]/td[2]"}).click
  no_more_upgrades_to_buy
end

def increment_wait_count
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[2]'}).click
  driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.click
  count = driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[2]/a/div[2]'}).text.to_i
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]'}).click
  if (count >= 30) then
    driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/*'}).last.click if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[4]/buyunit-input/div/buyunit/span/div/a[1]'}).last.text != "Can't uncocoon"
  end
  count
end

def increment_nightbugs
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[4]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]'}).click
  if driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.text != "Can't buy" then
    driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.click
  else
    sleep(12)
    driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).first.click
  end
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[6]/td[2]'}).click
end

def cocoon_larvae
  driver = DRIVER
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[2]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[2]/td[2]'}).click
  driver.find_elements({xpath: '/html/body/div[1]/div[6]/div/div/div/div[1]/unit/div[3]/buyunit-input/div/buyunit/span/div/*'}).last.click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div/div[2]/table/tbody/tr[3]/td[2]'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[4]/a'}).click
end

def do_all_the_things
  driver = DRIVER
  grow_all_the_meat
  multiply_all_the_larvae
  claim_all_the_territory
end

def do_all_the_things_without_warp_or_counting
  driver = DRIVER
  grow_all_the_meat_without_warp
  multiply_all_the_larvae
  claim_all_the_territory
end

def do_all_the_things_without_warp
  driver = DRIVER
  grow_all_the_meat_without_warp
  multiply_all_the_larvae
  count = increment_wait_count
  claim_all_the_territory
  count
end

def do_all_the_things_without_warp_or_twins
  driver = DRIVER
  grow_all_the_meat_without_warp
  multiply_all_the_larvae
  claim_all_the_territory_without_twins
end

def do_all_the_things_for_the_future
  driver = DRIVER
  grow_all_the_meat_for_the_future
  multiply_all_the_larvae_for_the_future
end

def import_swarm
  driver = DRIVER

  # get save file
  old_save = HTTParty.post("https://content.dropboxapi.com/2/files/download", headers: {'Authorization' => "Bearer ARClIUaWD-8AAAAAAAATnoDdImiaS7e2hg4fnsThvfDhnKHfZ4FOlUJn-g-DSCvo", "Dropbox-API-Arg" => "{\"path\": \"/#{FOLDER}/swarm.sav\"}", "Content-Type" => ""})

  # import save
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[6]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[6]/ul/li[5]/a'}).click
  driver.find_element({xpath: '//*[@id="export"]'}).clear
  # IO.popen(COPY_CMD, 'w') { |k| k << old_save }
  # driver.find_element({xpath: '//*[@id="export"]'}).send_keys(PASTE_KEYS)
  driver.execute_script("document.getElementById('export').value=arguments[0]", old_save)
  driver.find_element({id: 'export'}).send_keys([' ', :backspace])
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div[3]/label[2]/input'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div[4]/label[5]/input'}).click
end

def export_swarm
  driver = DRIVER

  # get new save
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[6]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/tabs/ul/li[6]/ul/li[5]/a'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div[3]/label[1]/input'}).click
  driver.find_element({xpath: '/html/body/div[1]/div[6]/div/div/div[4]/label[1]/input'}).click
  new_save = driver.find_element({xpath: '//*[@id="export"]'}).attribute('value')

  # export save
  HTTParty.post("https://content.dropboxapi.com/2/files/upload", headers: {'Authorization' => "Bearer ARClIUaWD-8AAAAAAAATnoDdImiaS7e2hg4fnsThvfDhnKHfZ4FOlUJn-g-DSCvo", "Dropbox-API-Arg" => "{\"path\": \"/#{FOLDER}/swarm.sav\", \"mode\": \"overwrite\"}", "Content-Type" => "text/plain; charset=dropbox-cors-hack"}, body: new_save)
  HTTParty.post("https://content.dropboxapi.com/2/files/upload", headers: {'Authorization' => "Bearer ARClIUaWD-8AAAAAAAATnoDdImiaS7e2hg4fnsThvfDhnKHfZ4FOlUJn-g-DSCvo", "Dropbox-API-Arg" => "{\"path\": \"/#{FOLDER}/swarm.sav.#{Time.new.to_i}\"}", "Content-Type" => "text/plain; charset=dropbox-cors-hack"}, body: new_save)
end

def to_standard_form(man, exp)
  while (man >= 10) do
    man = man / 10
    exp = exp + 1
  end
  [man.to_s, exp.to_s]
end
