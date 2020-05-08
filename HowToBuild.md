## Building the Plugin from Source ##

(*Full instructions all in one place*)

RabbitMQ must be installed before building the plugin. If the instructions do not work on a particular machine, you may be able to build the plugin elsewhere and copy it to the server.

1. Upgrade GNU Make by following the steps below. See [here](https://stackoverflow.com/questions/31912233/how-to-update-make-3-81-linux) for more details.

   1. Download:

      - `cd /tmp`
      - `target=make-4.2.1`
      - `wget http://ftp.gnu.org/gnu/make/$target.tar.gz`
      - `tar xvf $target.tar.gz`

   2. Build and install:

      - `cd $target`
      - `./configure`
      - `make`
      - `sudo make install`

   3. Check the version of make (4.2.1 as of 10/4/19):

      - `make -v`

   4. Remove the old make that was installed from the OS's repository:

      - `yum remove make`

2. Build and install Elixir.  See [here](https://elixir-lang.org/install.html#compiling-from-source-unix-and-mingw) for more details.

   1. Download:

      - `cd /tmp`
      - `git clone -b v1.8 --single-branch https://github.com/elixir-lang/elixir.git`

   2. Build and test:

      - `cd elixir`
      - `sudo make clean test`

   3. Add Elixir's bin path to your environment variables:

      - `export PATH="$PATH:/tmp/elixir/bin"`

   4. Check the version of elixir (1.8.2 as of 10/4/19):
Plugin Development Guide
      - `elixir -v`

3. Now make and install the plugin:

    - `git clone https://github.com/hnei/rabbitmq-message-timestamp`
    - `cd rabbitmq-message-timestamp`
    - `cat Makefile` and check that the update the current RabbitMQ version meets the *broker_version_requirements*. If not, you may need to get a new clone of [the original rabbitmq-message-timestamp repo](https://github.com/rabbitmq/rabbitmq-message-timestamp) and reapply the changes from ms to ns (search for "millis", "ms", and "Ms", and "MS" in the code) to meet the current RabbitMQ version.
    - `sudo make`
    - `sudo systemctl stop rabbitmq-server`
    - `sudo make run-broker` and check that it runs ok
    - `sudo make dist`
    - `cd plugins`
    - Copy the binary to the argems repository and the RabbitMQ server's plugins folder:
        - `sudo cp rabbitmq_message_timestamp-0.0.0.ez argems/setup/server/rabbitmq/plugins/rabbitmq_message_timestamp-$ver.ez` where $ver is the version of the RabbitMQ server
        - `(cd argems/setup/rabbitmq/plugins/; git add *.ez; sudo cp rabbitmq_message_timestamp-$ver.ez /usr/lib/rabbitmq/lib/rabbitmq_server-$ver/plugins)`
    - `sudo systemctl start rabbitmq-server`
    - `sudo rabbitmq-plugins list` and check that rabbitmq_message_timestamp is present but not enabled
    - `sudo rabbitmq-plugins enable rabbitmq_message_timestamp`
    - `sudo systemctl restart rabbitmq-server` to get the plugin to run immediately

## References ##

[RabbitMQ Plugin Development Guide](https://www.rabbitmq.com/plugin-development.html)
