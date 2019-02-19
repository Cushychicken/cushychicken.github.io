---
bg: "../charlatan.jpg"
layout: post
title: "Generating Demo Databases Quickly With Faker and Dataset"
tags: ['programming']
date: 2016-09-06
---
I've been throwing the odd spare hour at a side project that needs a backend database to test against. (Interested in using or writing an asset tracking app for circuit boards? Check out [trackrack](https://github.com/Cushychicken/trackrack) on Github.) Before this, I had just been using some junk data, but decided it'd be a better/more realistic test of the app to use some randomly generated data. It also gave me the chance to try out two software modules I've been wanting to mess around with for a while.

I had seen Ruby's `faker` module held up as a nice tool for generating structured random data at a coding meetup, but had never had reason to use it until now. It's a great one-stop-shop for generating email addresses, names, timestamps, and MAC addresses, which was just what I needed. Plus, if I ever want to go back and replace all the names generated with ones from _Game of Thrones_, it's already got that capability built in.

{% highlight ruby %}
require 'Faker'
require 'json'

def makeBoardInfo()
        boardInfo = []
        (0..rand(25)).each do |counter|
        		now = DateTime.now
                data = {
                        "info" => Faker::Lorem.sentence,
                        "time" => Faker::Time.between(now - 7, now)
                }
                boardInfo << data
        end
        return boardInfo
end

def makeTestDataset()
        boardLotInfo = {}
        (0..rand(25)).each do |counter|
                length = 13
                board_no = rand(36**length).to_s(36)
                now = DateTime.now
                data = {
                        "owner"       => Faker::Name.name,
                        "last_update" => Faker::Time.between(now - 1, now),
                        "mac_addr"    => Faker::Internet.mac_address,
                        "notes"       => makeBoardInfo()
                }
                boardLotInfo[board_no] = data
        end
        return boardLotInfo
end

data = makeTestDataset()
File.open("demo_data.json", "w") do |f|
        f.write(data.to_json)
end
{% endhighlight %}

Instead of a database, this little Ruby script just generates a JSON file as output. This was by design; the app, as written now, relies on a big JSON file in lieu of a database. Additionally, the second module I wanted to use is a Python package designed to ease database creation and use. Converting the flat file into a database seemed like the ideal dry run for the `dataset` Python module.

{% highlight python %}
import dataset
import json

with open('demo_data.json') as f:
    data = json.load(f)

db = dataset.connect('sqlite:///demo_board.db')
table = db['boards']
notes = db['notes']
for entry in data:
    table.insert(dict(board_no    = entry,
                      last_update = data[entry]['last_update'],
                      mac_addr    = data[entry]['mac_addr'],
                      owner       = data[entry]['owner']))
    for note in data[entry]['notes']:
        notes.insert(dict(board_no = entry,
                          info     = note['info'],
                          time     = note['time']))
{% endhighlight %}

Note here that none of the code implemented with `dataset` looks like a SQL statement. As far as I'm concerned, that's OK! I know next to nothing about SQL. Which makes `dataset`'s hand-holdy approach doubly convenient: it hides just about all of the SQL-y stuff away under a nice Python abstraction and leaves me with a nice `.db` file to drop into the application. (...provided that I ever figure out enough about `dataset` to need it, anyway.)

I'd have liked to have written this app as an end-to-end Python application, but I had issues installing the Python port of Faker. Something to do with importing an `ipaddress` module...? Also found a few GitHub issues saying it wasn't backwards compatible with Python 2. Yeah, yeah, I get it, I should just migrate to 3 already... In the meantime, however, it was nothing that forty-odd lines of Ruby couldn't handle.

Now I just have to figure out enough about `dataset` to actually use this database in the app backend... More to come. 
