---
id: connection-between-browser-and-node
title: Front-end and Back-end Connection Implementation
slug: connection-between-browser-and-node
order: 6
---

To implement two-way communication between the front and back ends, we rely on special service declarations of 'BrowserModule' and 'NodeModule'.

First of all, you need to define a single channel for two-way communication message. We define a `ITodoConnectionServerPath` as the only ID message channel. Front and back ends distributes the service through the channel.

```ts
// modules/todo/common/index.ts

export const ITodoConnectionServerPath = 'ITodoConnectionServerPath';
```

## Associating Front-end and Back-end Services

First, you need to inherit 'RPCService' on `TodoService` and `TodoNodeService`.

```ts
// modules/todo/browser/todo.service.ts

@Injectable()
export class TodoService extends RPCService implements ITodoService { ... }
```

```ts
// modules/todo/node/todo.service.ts

@Injectable()
export class TodoNodeService extends RPCService implements ITodoNodeService { ... }
```

Associate on a double end `TodoListModule` .

### Associated Front-end Service

```ts
// modules/todo/browser/index.ts

import { Provider, Injectable } from '@opensumi/di';
import { BrowserModule } from '@opensumi/ide-core-browser';
import { TodoContribution } from './todo.contribution';
import { TodoService } from './todo.service';
import { ITodoConnectionServerPath, ITodoService } from '../common';

@Injectable()
export class TodoListModule extends BrowserModule {
  providers: Provider[] = [
    {
      token: ITodoService,
      useClass: TodoService
    },
    TodoContribution
  ];

  backServices = [
    {
      servicePath: ITodoConnectionServerPath, // The only path of the two-end communication channel
      clientToken: ITodoService // Associated front-end service
    }
  ];
}
```

### Associate Front-end and Back-end Services

```ts
// modules/todo/node/index.ts

import { Provider, Injectable } from '@opensumi/di';
import { NodeModule } from '@opensumi/ide-core-node';
import { ITodoNodeService, ITodoConnectionServerPath } from '../common';
import { TodoNodeService } from './todo.service';

@Injectable()
export class TodoListModule extends NodeModule {
  providers: Provider[] = [
    {
      token: ITodoNodeService,
      useClass: TodoNodeService
    }
  ];

  backServices = [
    {
      servicePath: ITodoConnectionServerPath, // The only path of the two-end communication channel
      token: ITodoNodeService // Associated back-end service
    }
  ];
}
```

## Implement Front-end and Back-end Services

This case makes it possible that while the Todo item is clicked, a message is passed to the back-end service. Upon receiving the message, the back-end service reorganizes the content of the message and passes it back to the front-end for message display. The core implementation is as follows.

The front-end service notifies the back-end service as it displays the message.

```ts
// modules/todo/browser/todo.service.ts

@Injectable()
export class TodoService extends RPCService implements ITodoService {
  ...
  @Autowired(ITodoConnectionServerPath)
  private todoNodeService: ITodoNodeService;

  // Display the message when the backend service is invoked
  showMessage = (message: string) => {
    this.messageService.info(message);
    this.todoNodeService.showMessage(message);
  };
  // Receive back end messages
  onMessage = (message: string) => {
    this.messageService.info(message);
  };
  ...
}
```

The back-end service receives the message and sends it back to the front-end service.

```ts
// modules/todo/node/todo.service.ts

import { Injectable } from '@opensumi/di';
import { ITodoNodeService } from '../common';
import { RPCService } from '@opensumi/ide-connection';

@Injectable()
export class TodoNodeService extends RPCService implements ITodoNodeService {
  showMessage = (message: string) => {
    // Here, this.rpcClient![0] can directly obtain the proxy instance under the communication channel
    this.rpcClient![0].onMessage(`I got you message, echo again. ${message}`);
  };
}
```

## Results Show

![Two-way dispaly](https://img.alicdn.com/imgextra/i1/O1CN01ItcgHk1l0kmoQIjmb_!!6000000004757-1-tps-1200-706.gif)

Since then, we have completed the whole case instruction.
