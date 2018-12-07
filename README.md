scaredboard-deploy
==================

**scaredboard-deploy** provides a dockerized reference deployment setup of the [sacredboard](https://github.com/chovanecm/sacredboard) dashboard for the [sacred](https://github.com/IDSIA/sacred) ML experiment management framework.

Instructions
------------

### Prerequisites

[Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/) are required. Although the setup only has been explicitly tested against Docker version 18.06.1-ce, most other recent Docker versions should be supported too.

### Project Structure

A custom Docker image for sacredboard is defined in `Dockerfile.sacredboard`. This image may also be used independently of the remaining setup, e.g. with a custom MongoDB instance. For this purpose, the MongoDB url and database name with which sacredboard is started can be configured using the `MONGODB_URL` and `MONGODB_DATABASE` environment variables.

`docker-compose.yml` defines two services. A sacredboard container based on the provided sacredboard image, publishing on the `5000` port and a MongoDB instance with persistent data storage in a volume, publishing on the host port `27017`.

### Installation & Running

To clone the project and start the sacredboard stack in detached mode, the following commands must be executed:
```bash
git clone https://github.com/kklemon/sacredboard-deploy.git
cd sacredboard-deploy/
docker-compose up -d
```

When the containers are spun up, sacredboard should be available on `localhost:5000`.

### Usage

To save results of sacred experiments into the provided MongoDB instance and make them visible in sacredboard, a MongoObserver has to be added to an experiment, configured with `localhost:27017` as url and `sacred` as database name. An exemplary experiment configuration may look like as follows:
```python
from sacred import Experiment
from sacred.observers import MongoObserver

ex = Experiment('my-experiment')
ex.observers.append(MongoObserver.create(url='localhost:27017',
                                         db_name='sacred'))
``` 

Alternatively, an MongoObserver can be configured through the command line by running the experiment with the following option:
```bash
./my_experiment.py -m localhost:27017:sacred
```
