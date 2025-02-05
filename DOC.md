# Documentation

## Container Configuration
```js
// You can either build containers using docker compose OR set up multiple images
// using SingleContainerConfig, but not both
export type JestTestcontainersConfig =
  | DockerComposeContainersConfig
  | MultipleContainerConfig;

export type DockerComposeConfig = {
  // The directory where the compose file is located
  composeFilePath: string;
  // The actual docker compose file to be built
  composeFile: string;
  startupTimeout?: number;
}

// you can have multiple containers that get started with your test
type MultipleContainerConfig = {
  // each container needs to have a unique key
  // the IP and PORTS for this container will be registered with this container key
  // for example, with a *containerKey* of redis, you would end up with
  // global.__TESTCONTAINERS_REDIS_IP__ and global.__TESTCONTAINERS_REDIS_PORT_XXXX__
  // variables
  [key: string]: SingleContainerConfig;
};

export interface SingleContainerConfig {
  image: string;
  tag?: string;
  // for each port, a host port will be mount randomly
  // and the random port number will be registered to the global variable using the containerKey and original port number
  // so if you put [6379] here, and you have a *containerKey* of redis, you will have
  // global.__TESTCONTAINERS_REDIS_PORT_6379__
  // set to the random host port that the currently running container is bound to for 6379
  ports?: number[];
  // created container will have the name specified here
  // this is introduced so you can have prefix to your containers
  // for cleanup logic while you are using docker on docker
  name?: string;
  // environment variables can be set for the container. this is a key/value string map 
  env?: EnvironmentVariableMap;
  // when to start your tests? how to make sure container is running?
  // see below for options
  wait?: WaitConfig;
  // array of mounts to bind local (host's) files or directories into the container file system
  // see below to know how to specify a bind
  bindMounts?: BindMount[];
}

export type EnvironmentVariableMap = { [key: string]: string };
export type WaitConfig = PortsWaitConfig | TextWaitConfig | CombinedWaitConfig;

// wait for host and internal ports to be connectable
// default behaviour is this
interface PortsWaitConfig {
  type: "ports";
  // how many seconds to wait for the sockets to be available before we timeout?
  // default is 60 seconds
  timeout: number;
}

// wait until you see a text in the console output of the container
interface TextWaitConfig {
  type: "text";
  // part of the string that will be seen on the console output line
  text: string;
}

// wait until you see a text in the console output of the container in repeatCount time
// and wait for timeout time
interface CombinedWaitConfig {
  type: "combined";
  text: string;
  timeout: number;
  repeatCount: number;
}

export interface BindConfig {
  // path to the host machine's path to bind
  source: string;
  // path to the guest machine (container) where it will be bound
  target: string;
  // whether if we grant wrant permissions
  mode: BindMode;
}

// permissions of the bound directory / file
// ro: readonly
// rw: read and write
export type BindMode = "ro" | "rw";

```
