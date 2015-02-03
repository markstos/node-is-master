is-master
=========

Find the master node process in a multi server cluster.

This module finds the master node in a cluster by inserting the node in a mongodb and choosing the master by which node is the oldest. Each node checks into mongodb on a set time (default 1 minute). If the master node dies for whatever reason, mongodb will expire the record and the next node in line will become the master.

Use cases for this module are if you run your node cluster with a cluster manager like PM2 or even if you run your clusters on multiple servers (they just need to all report into the same mongodb). This will allow you to assign one node process as the master so that it can run tasks that should only be ran by one process, such as scheduled tasks and database cleanup.

## Installation

  npm install is-master --save

## Usage

    var mongoose = require('../node_modules/mongoose');
    var im = require('../is-master.js');

    // Start the mongoose db connection
    mongoose.connect('mongodb://127.0.0.1:27017/im', function(err) {
        if (err) {
            console.error('\x1b[31m', 'Could not connect to MongoDB!');
            throw (err);
        }
    });

    // Start the is-master worker
    im.start();

    // Check if this current process is the master every 5 seconds
    setInterval(function() {
        im.isMaster(function(err, results) {
            if (err) return console.error(err);
            console.log(results);
        });
    }, 5000);

## Options

When starting the worker, you can specify option in an object to update the default values.

    im.start({
        timeout: 120, // This value is in seconds
        hostname: 'devServer1'
    });

## FAQ

Q. I updated the timeout option, but mongodb is not expiring the node in that timeout specified.

A. 60 seconds is added to the mongodb expire timeout to ensure the master has time to checkin. Also please note, if this value is changed from the initial creation of the table, it will not be able to update the index. You will need to delete the table and then restart your server to re-create it.

## Tests

  npm test

## Contributing

In lieu of a formal styleguide, take care to maintain the existing coding style.
Add unit tests for any new or changed functionality. Lint and test your code.

## Release History

* 1.0.0 Initial release
