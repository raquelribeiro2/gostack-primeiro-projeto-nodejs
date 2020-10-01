# 🚀 Bootcamp GoStack 2020 - Primeiro Projeto NodeJS

## Executar projeto do repositório

- Abra a pasta no Visual Studio Code e execute o comando ``` yarn ``` ou ``` npm install ``` para instalar as dependências. 
- Os testes podem ser feitos no Insomnia, logo após iniciar o servidor com o comando ``` yarn dev:server ```

# Conteúdo aprendido neste módulo

## Configurando Estrutura

- Crie um arquivo, acesse-o pelo terminal e execute:

```jsx
$ yarn init -y
```

- Isso irá gerar o arquivo *package.json* dentro do seu projeto.
- Abra-o no Visual Studio Code e instale as primeiras ferramentas:

```jsx
$ yarn add express

$ yarn add typescript -D
```

- Depois para gerar o arquivo *tsconfig.json*, execute:

```jsx
$ yarn tsc --init
```

- É neste arquivo que ficam armazenadas as configurações de como o Typescript vai ser executado dentro do projeto.
- Posso setar duas configurações neste arquivo:

```json
// local onde ficarão os arquivos transpilados para js
"outDir": "./dist",                        
// local onde ficará o projeto
"rootDir": "./src",
```

- Depois, para transpilar o código, execute:

```jsx
$ yarn tsc
```

- Isso já criará a pasta *dist*.
- Para instalar o pacote com declarações de tipo do *express*, execute:

```jsx
$ yarn add @types/express -D
// sempre que uma lib acusar erro, podemos fazer a instalação desse pacote
// de declarações de tipo
// o padrão é yarn add @types/nome-da-lib -D
// sempre instalar como dependência de desenvolvimento
```

- Podemos criar um arquivo *server.ts* dentro da pasta *src*:

```jsx
import express, { request } from 'express';

const app = express();

app.get('/', (request, response) => {
  return response.json({ message: 'Olá!'});
});

app.listen(3333, () => {
  console.log('Server started on port 3333');
});
```

- Executar o comando  *yarn tsc* para transpilar o arquivo *ts* para *js*.
- Depois para iniciar o servidor, execute:

```jsx
$ node dist/server.js
```

- Agora em *localhost:3333* sua mensagem aparecerá.
- Para que não seja necessário a criação do dist toda vez que formos rodar nossa aplicação, podemos fazer de outra forma enquanto estamos em ambiente de desenvolvimento.
- Podemos entrar apagar a pasta *dist* e executar:

```jsx
$ yarn add ts-node-dev -D
```

- Com isso podemos criar os scripts dentro do *package.json*:

```json
"scripts": {
    "build": "tsc",
// Ele faz o papel do tsc que converte ts para js e também do nodemon, que faz o
// reload de forma automática quando detecta alteração no código
    "dev:server": "ts-node-dev --transpileOnly --ignore-watch node_modules src/server.ts"
  },
```

- Existem dois parâmetros que podemos passar na execução deste script que o deixarão muito mais rápida:
    - **--transpileOnly:** não verifica se o código está certo ou não, ele vai apenas executá-lo. Quem irá ficar com a responsabilidade de dizer se nosso código está correto ou não, será o próprio Visual Studio Code.
    - **--ignore-watch node_modules:** ele evita que o nosso ts-node-dev tente compilar os códigos que estão dentro da pasta node-modules ou fique observando alterações neste código.

- Agora para iniciar o servidor basta executar:

```jsx
$ yarn dev:server
```

## Padrões de Projeto com ESLint, Prettier e EditorConfig

Siga o tutorial que se encontra no link abaixo:

- [Configuração ESLint, Prettier e EditorConfig](https://www.notion.so/Padr-es-de-projeto-com-ESLint-Prettier-e-EditorConfig-0b57b47a24724c859c0cf226aa0cc3a7)


## Debugando o NodeJS

## Configuração do Debug

- Primeiramente, no Visual Studio Code, do lado esquerdo onde há uma imagem de debug com play, clique em **Create a launch.json.file** para acessar as configurações do Debug, que devem ficar da seguinte forma:

```json
{
  // Use IntelliSense to learn about possible attributes.
  // Hover to view descriptions of existing attributes.
  // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
// ao executar o debug, ele irá tentar se conectar à aplicação que está rodando 
      "request": "attach",
// é uma forma de inspecionar o node
      "protocol": "inspector",
      "restart": true,
      "name": "Debug",
      "skipFiles": [
        "<node_internals>/**"
      ],
      "outFiles": [
        "${workspaceFolder}/**/*.js"
      ]
    }
  ]
}
```

- Volte ao arquivo *package.json*  e adicione a flag --inspect ao script **dev:server**:

```json
"scripts": {
    "build": "tsc",
    "dev:server": "ts-node-dev --inspect --transpile-only --ignore-watch node_modules src/server.ts"
  },
```

- A partir deste momento, quando o servidor é iniciado, o debug já consegue se conectar à aplicação e conseguimos iniciar o debug.
- Crie um arquivo *index.ts* dentro da pasta *routes*, com o seguinte conteúdo:

```tsx
import { Router } from 'express';

const routes = Router();

routes.post('/users', (request, response) => {
  const { name, email } = request.body;

  const user = {
    name,
    email,
  };

  return response.json(user);
});

export default routes;
```

- Seu arquivo de server ficará da seguinte forma:

```tsx
import express from 'express';
import routes from './routes';

const app = express();

app.use(express.json());
app.use(routes);

app.listen(3333, () => {
  console.log('Server started on port 3333');
});
```

## Testando o Debug

- Imagine que você queira debugar seu código, para ver o conteúdo do *request.body*.
- Inicie seu servidor e aperte o botão Run em Debug.
- Agora adicione uma bolinha vermelha na linha que deseja debugar e salve o arquivo novamente.

![image](https://user-images.githubusercontent.com/57918707/94745438-12f1c180-0351-11eb-9976-db7db98503d3.png)

- Para testar, vá até o Insomnia e crie uma requisição com as informações de **name** e **email** no body. Assim que enviar a requisição, o debug te retornará automaticamente para o Visual Studio Code, onde poderá ver na parte de **Variables** todas as suas variáveis Locais e Globais.
- Se expandir o request, poderá ver as informações.

![image](https://user-images.githubusercontent.com/57918707/94745521-3caae880-0351-11eb-9c69-cdaac4fe58f2.png)

- Na parte de Debug também há a funcionalidade de **Watch**, onde podem ser adicionadas as variáveis que você deseja saber o conteúdo.

![image](https://user-images.githubusercontent.com/57918707/94745568-54826c80-0351-11eb-82a6-6e8a104e6e0e.png)

- E ainda a funcionalidade de **Call Stack** que mostra todas as funções que foram executados para processar nossa requisição.

![image](https://user-images.githubusercontent.com/57918707/94745590-65cb7900-0351-11eb-9fd8-a1bf611fdc93.png)

- O **Loaded Scripts** mostra quais foram os scripts carregados.

![image](https://user-images.githubusercontent.com/57918707/94745626-74b22b80-0351-11eb-8f3f-daaaffd3ee65.png)

- E nos **Breakpoints** temos a opção de marcar ou desmarcar algum breakpoint que já tenha adicionado a algum arquivo ou ainda marcar as opções Caught Exception (exceções) ou Uncaught Exception (exceções que não são capturadas por nós).
- Esses últimos dois servem para descobrirmos no momento que acontecer uma exceção, o porquê que aquilo aconteceu, pois por padrão, as exceções vão derrubar o servidor node.

![image](https://user-images.githubusercontent.com/57918707/94745699-927f9080-0351-11eb-850d-b63d87185041.png)


# Construindo a Aplicação

## Cadastro de Agendamentos

## Testando a rota de agendamentos

- Para iniciar, crie um arquivo *appointments.routes.ts* dentro da pasta *routes*; nele deve conter o seguinte código:

```tsx
import { Router } from 'express';

const appointmentsRouter = Router();

appointmentsRouter.post('/', (request, response) => {
  return response.json({ message: 'Olá!!' });
});

export default appointmentsRouter;
```

- No arquivo *index.ts* será onde ficarão as chamadas das rotas:

```tsx
import { Router } from 'express';
import appointmentsRouter from './appointments.routes';

const routes = Router();

// O use fará com que toda rota que iniciar com /appointments, independente do método
// GET, POST, PUT, DELETE, ou seja, ele funciona para qualquer método
// Sendo assim, tudo o que vier depois do /appointments será repassado para dentro
// de appointmentsRouter, fazendo com que quando for acessada a rota
// http://localhost:3333/appointments seja chamado o "arquivo" acima
routes.use('/appointments', appointmentsRouter);

export default routes;
```

- Após iniciar o servidor, poderá testar no Insomnia, obtendo o seguinte resultado:

![image](https://user-images.githubusercontent.com/57918707/94745214-a8408600-0350-11eb-837b-3f3ba2c044a5.png)

## Salvando um agendamento dentro da Aplicação

- Como ainda não temos persistência no banco de dados, vamos fazer como no módulo anterior, utilizando um array, ou seja, nossos agendamentos serão salvos em memória.
- Precisaremos de uma biblioteca que gera ID automaticamente, para instalá-la, execute:

```tsx
$ yarn add uuidv4
```

- Sendo assim, o código ficará da seguinte forma:

```tsx
import { Router } from 'express';
import { uuid } from 'uuidv4';

const appointmentsRouter = Router();

const appointments = [];

appointmentsRouter.post('/', (request, response) => {
  const { provider, date } = request.body;

  const appointment = {
    id: uuid(),
    provider,
    date,
  };

  appointments.push(appointment);

  return response.json(appointment);
});

export default appointmentsRouter;
```

- No Insomnia, deveremos passar *provider*  e *date* no corpo da requisição.
- O Insomnia já existe uma função para que seja criada um data no formato Timestamp ISO 8601, que já retorna a data no formato americano.

![image](https://user-images.githubusercontent.com/57918707/94745260-bd1d1980-0350-11eb-91dd-706c5adfe062.png)


## Validando a Data

- Para começar, vamos instalar uma biblioteca para lidar com datas e horários dentro do Javascript:

```tsx
$ yarn add date-fns
```

- Agora importe duas funções que serão utilizadas:

```tsx
// A função parseISO irá converter uma string para um formato date
// O startOfHour vai colocar minuto, segundo, milisegundo como zero, ou seja
// início daquela hora
import { startOfHour, parseISO } from 'date-fns';
```

- Vamos salvar a informação de date já configurada na constante parsedDate e o enviaremos no lugar de date que está no body, pois já estará tratado:

```tsx
import { Router } from 'express';
import { uuid } from 'uuidv4';
import { startOfHour, parseISO } from 'date-fns';

const appointmentsRouter = Router();

const appointments = [];

appointmentsRouter.post('/', (request, response) => {
  const { provider, date } = request.body;

// criação dessa constante, onde pegará a informação de date, transformará para
// tipo date (parseISO) e colocará a hora como exata (8:00:00, por exemplo)
  const parsedDate = startOfHour(parseISO(date));

  const appointment = {
    id: uuid(),
    provider,
// Vamos enviar parsedDate, pois já estará tratado
    date: parsedDate,
  };

  appointments.push(appointment);

  return response.json(appointment);
});

export default appointmentsRouter;
```

- Quando testamos no Insomnia, podemos notar que a hora agora veio exata:

![image](https://user-images.githubusercontent.com/57918707/94867852-6418b880-0418-11eb-825b-29c92af4311b.png)

- Agora colocaremos a condição que dois agendamentos não podem ser criados no mesmo horário:

```tsx
import { Router } from 'express';
import { uuid } from 'uuidv4';
import { startOfHour, parseISO, isEqual } from 'date-fns';

const appointmentsRouter = Router();

// Para resolver o erro que está sendo acusado pelo typescript, onde appointments 
// tem um tipo qualquer (any), ou seja, não conseguimos definir o tipo dessa variável,
// logo, não sabemos quais campos teremos dentro da variável, por isso, é preciso
// adicionar tipagem para esta variável, para isso, foi criada a interface Appointment
interface Appointment {
  id: string;
  provider: string;
  date: Date;
}

// Estamos dizendo que o tipo da variável appointments é um array de Appointment, ou
// seja, o conteúdo dela é o que está descrito dentro de Appointment
const appointments: Appointment[] = [];

appointmentsRouter.post('/', (request, response) => {
  const { provider, date } = request.body;

  const parsedDate = startOfHour(parseISO(date));
// O método find retorna true ou false baseada na condição que foi passada
// Para cada um dos agendamentos, faremos a comparação de datas utilizando a função
// isEqual, que verifica se as duas datas estão no mesmo dia e horário
  const findAppointmentInSameDate = appointments.find(appointment =>
    isEqual(parsedDate, appointment.date),
  );

// Se for encontrado, retornará um erro, de que este horário já está ocupado
  if (findAppointmentInSameDate) {
    return response
      .status(400)
      .json({ message: 'This appointment is already booked.' });
  }

  const appointment = {
    id: uuid(),
    provider,
    date: parsedDate,
  };

  appointments.push(appointment);

  return response.json(appointment);
});

export default appointmentsRouter;
```

- Após tentativa de criar dois agendamentos no mesmo horário, aparecerá a mensagem:

![image](https://user-images.githubusercontent.com/57918707/94867898-798de280-0418-11eb-9596-731646252294.png)


## Model de Agendamento

- O primeiro conceito que vamos aplicar dentro da nossa estrutura para começar a isolar um pouco as responsabilidades, é o conceito de **Model** ou **Entidade**.
- O **Model** ou **Entidade** é uma forma de armazenar os dados, é o formato de um dado que é armazenado em algum lugar, como um banco de dados ou a própria memória.
- Os Models vão definir também qual é o formato desse dado.
- Para que não tenhamos que ficar importando e exportando as interfaces para serem usadas em outros arquivos, vamos criar um pasta em *src*, chamada *models*, e dentro dela, criar um arquivo *Appointment.ts*.
- Vamos escrever essa entidade em formato de classe :

```tsx
import { uuid } from 'uuidv4';

class Appointment {
  id: string;

  provider: string;

  date: Date;

// O método constructor serve para quando escrevermos *new Appointment()* possamos
// passar alguns parâmetros para criar o Appointment já baseados em algumas 
// informações pré existentes

// Quando o método constructor for chamado, ele vai receber provider e date
// Temos que passar o tipo dessas variáveis
  constructor(provider: string, date: Date) {
// Vamos setar as variáveis com esses nomes
		this.id = uuid();
		this.provider = provider;
		this.date = date;
  }
}

export default Appointment;
```

- Feito isso podemos tirar a *interface* de dentro do arquivo *appointment.routes.ts* e apenas importar o arquivo *Appointment.ts* dentro dele.

```tsx
import Appointment from '../models/Appointment';
```

- E no momento da criação de um appointment, ao invés de passar um objeto, vou passar o *new Appointment* que chamará o método construtor:

```tsx
// Antes
const appointment = {
    id: uuid(),
    provider,
    date: parsedDate,
  };

// Depois
const appointment = new Appointment(provider, parsedDate);
```

- Podemos retirar também a importação da lib uuid, pois essa responsabilidade de criar ID está no model Appointment.


## Criando Repositórios

- O próximo conceito que iremos aplicar para melhorar ainda mais nossa estrutura e organização dos arquivos é o conceito de Repositório.
- Podemos pensar no Repositório como uma conexão entre a Persistência (dos dados) e as Rotas.
- Dentro do Repositório é onde conseguiremos buscar as informações que estão, por exemplo, persistidas dentro de um banco ou dentro de uma variável.
    - Então poderemos ter, por exemplo, um método *find*, que vai procurar por informações.

- Também teremos o método *create*, então poderemos criar uma informação.
- Teremos um repositório por model.
- Ele será responsável basicamente pelo CRUD, ou seja, criar, ler, editar e deletar informações.
- Também o criaremos em formato de classes.

```tsx
import { isEqual } from 'date-fns';
import Appointment from '../models/Appointment';

class AppointmentRepository {
// O private indica que appointments não é uma variável que será acessível por
// fora da classe 
  private appointments: Appointment[];

	constructor() {
		this.appointments = [];
	}

// Criação de um método publico que irá encontrar um Appointment em uma data
// específica, que é para conseguirmos fazer a validação
// Então vou receber a data e retornar o Appointment, caso eu o encontre
// Caso não encontre, retornará null
	public findByDate(date: Date): Appointment | null {
    const findAppointment = this.appointments.find(appointment =>
      isEqual(date, appointment.date),
    );

    return findAppointment || null;
  }

// O public indica que será possível acessá-lo por fora da classe
// Quando criamos métodos dentro de uma classe, é altamente recomendável, colocarmos
// de forma fixa, qual o retorno que o método terá, ou seja, se ele está retornando
// no final um appointment que é um new Appointment que acabamos de criar, o retorno
// será um objeto do mesmo formato que o model de Appointment
 	public create(provider: string, date: Date): Appointment {
		const appointment = new Appointment(provider, date);

// Pegar this.appointments que é a lista e fazer um .push, ou seja, salvar esse
// appointment dentro da minha lista 
    this.appointments.push(appointment);

		return appointment;
	}
}

export default AppointmentsRepository;
```

- Voltamos até o arquivo *appointments.routes.ts* e podemos retirar a variável de appointments;
- Importamos o *ApointmentsRepository* e criamos outra variável:

```tsx
import AppointmentsRepository from '../repositories/AppointmentsRepository';

// Estamos instanciando aquela classe
const appointmentsRepository = new AppointmentsRepository();
```

- O código neste arquivo ficará da seguinte forma:

```tsx
import { Router } from 'express';
import { startOfHour, parseISO } from 'date-fns';

import AppointmentsRepository from '../repositories/AppointmentsRepository';

const appointmentsRouter = Router();
const appointmentsRepository = new AppointmentsRepository();

appointmentsRouter.post('/', (request, response) => {
  const { provider, date } = request.body;

  const parsedDate = startOfHour(parseISO(date));

  const findAppointmentInSameDate = appointmentsRepository.findByDate(
    parsedDate,
  );

  if (findAppointmentInSameDate) {
    return response
      .status(400)
      .json({ message: 'This appointment is already booked.' });
  }

  const appointment = appointmentsRepository.create(provider, parsedDate);

  return response.json(appointment);
});

export default appointmentsRouter;
```

## Listando Agendamentos

- Faremos agora uma rota de listagem de agendamentos:

```tsx
// Arquivo AppointmentsRepository.ts

import { isEqual } from 'date-fns';
import Appointment from '../models/Appointment';

class AppointmentRepository {
  private appointments: Appointment[];

  constructor() {
    this.appointments = [];
  }

// Método all, que retornará todos os agendamentos
  public all(): Appointment[] {
		return this.appointments;
}

  public findByDate(date: Date): Appointment | null {
    const findAppointment = this.appointments.find(appointment =>
      isEqual(date, appointment.date),
    );

    return findAppointment || null;
  }

  public create(provider: string, date: Date): Appointment {
    const appointment = new Appointment(provider, date);

    this.appointments.push(appointment);

    return appointment;
  }
}

export default AppointmentRepository;
```

```tsx
// Arquivo appointments.routes.ts

import { Router } from 'express';
import { startOfHour, parseISO } from 'date-fns';

import AppointmentsRepository from '../repositories/AppointmentsRepository';

const appointmentsRouter = Router();
const appointmentsRepository = new AppointmentsRepository();

appointmentsRouter.get('/', (request, response) => {
  const appointments = appointmentsRepository.all();

  return response.json(appointments);
});

appointmentsRouter.post('/', (request, response) => {
  const { provider, date } = request.body;

  const parsedDate = startOfHour(parseISO(date));

  const findAppointmentInSameDate = appointmentsRepository.findByDate(
    parsedDate,
  );

  if (findAppointmentInSameDate) {
    return response
      .status(400)
      .json({ message: 'This appointment is already booked.' });
  }

  const appointment = appointmentsRepository.create(provider, parsedDate);

  return response.json(appointment);
});

export default appointmentsRouter;
```

- Inicie o servidor e teste pelo Insomnia; o resultado deve ser parecido com este:

![image](https://user-images.githubusercontent.com/57918707/94868030-bb1e8d80-0418-11eb-97bd-059be8228b4c.png)


## Trabalhando com Dados

- Quando passamos uma informação de um arquivo para outro, é chamado de DTO ( Data Transfer Object), ou seja, estamos transferindo um objeto de dados.
- Sempre que transferimos dados de um arquivo para outro é legal utilizarmos objeto no Javascript, ainda mais se for um dado que não é único, que não contém uma única informação.

```tsx
import { isEqual } from 'date-fns';
import Appointment from '../models/Appointment';

interface CreateAppointmentDTO {
  provider: string;
  date: Date;
}

class AppointmentRepository {
  private appointments: Appointment[];

  constructor() {
    this.appointments = [];
  }

  public all(): Appointment[] {
    return this.appointments;
  }

  public findByDate(date: Date): Appointment | null {
    const findAppointment = this.appointments.find(appointment =>
      isEqual(date, appointment.date),
    );

    return findAppointment || null;
  }

// Mudamos para uma estrutura de parâmetros nomeados, ou seja, ao invés de enviarmos
// na rota cada parâmetro separado por vírgula, em um argumento da função, nós 
// enviaremos um objeto 
  public create({ provider, date }: CreateAppointmentDTO): Appointment {
    const appointment = new Appointment(provider, date);

    this.appointments.push(appointment);

    return appointment;
  }
}

export default AppointmentRepository;
```

- Ficará da seguinte forma:

```tsx
import { Router } from 'express';
import { startOfHour, parseISO } from 'date-fns';

import AppointmentsRepository from '../repositories/AppointmentsRepository';

const appointmentsRouter = Router();
const appointmentsRepository = new AppointmentsRepository();

appointmentsRouter.get('/', (request, response) => {
  const appointments = appointmentsRepository.all();

  return response.json(appointments);
});

appointmentsRouter.post('/', (request, response) => {
  const { provider, date } = request.body;

  const parsedDate = startOfHour(parseISO(date));

  const findAppointmentInSameDate = appointmentsRepository.findByDate(
    parsedDate,
  );

  if (findAppointmentInSameDate) {
    return response
      .status(400)
      .json({ message: 'This appointment is already booked.' });
  }

// Desestruturamos o envio da criação, assim ficará mais fácil, caso seja adicionado
// mais algum campo, por exemplo
  const appointment = appointmentsRepository.create({
    provider,
    date: parsedDate,
  });

  return response.json(appointment);
});

export default appointmentsRouter;
```

- No Model, podemos utilizar um função helper chamada *Omit<>*, onde dentro dos sinais <> são passados dois parâmetros, sendo o primeiro deles o tipo daquele objeto e o segundo, qual é a variável que desejo omitir desse tipo.
- Poderíamos ter feito um *interface*, porém isso dificultaria no futuro, quando novos campos fossem adicionados, tendo que ser passados dentro do *interface* também. Usando o *Omit*, só omitimos o campo que não queremos, e posteriormente, caso algum campo seja adicionado, ele já estará disponível no objeto.

```tsx
import { uuid } from 'uuidv4';

class Appointment {
  id: string;

  provider: string;

  date: Date;

  constructor({ provider, date }: Omit<Appointment, 'id'>) {
    this.id = uuid();
    this.provider = provider;
    this.date = date;
  }
}

export default Appointment;
```

- E agora no *AppointmentRepository* na parte de criação de agendamentos, desestruturamos, deixando provider e date como objeto:

```tsx
public create({ provider, date }: CreateAppointmentDTO): Appointment {
    const appointment = new Appointment({ provider, date });

    this.appointments.push(appointment);

    return appointment;
}
```

## Services e SOLID

- Começamos criando uma pasta *services*, que irá armazenar a regra de negócio da nossa aplicação.
- No momento, nossa rota está com muitas responsabilidades, sendo que ela só deve se preocupar em receber a requisição, chamar outro arquivo para tratar aquela requisição e devolver uma resposta. Se nossa rota estiver com algo além disso, devemos abstrair essa lógica dentro de um service.
- Um *service* sempre deve ter uma única e exclusiva funcionalidade.
- Criamos na nossa aplicação o princípio de Dependency Inversion, que preza que sempre que nosso service tiver uma dependência externa, como é o caso do *appointmentsRepository*, ao invés de instanciarmos uma nova classe de Repositório dentro do service, iremos receber o *appointmentsRepository* como um parâmetro da nossa classe, do nosso constructor.
- Isso vai facilitar para que nossa aplicação independente de quantos services diferentes estejam trabalhando com a parte de Appointment, todos eles estejam utilizando o mesmo Repositório de appointments e não repositórios diferentes, criados em cada um dos services

```tsx
import { startOfHour } from 'date-fns';

import Appointment from '../models/Appointment';
// Vamos importar o AppointmentsRepository pois precisamos falar que o tipo do parâmetro dentro do
// constructor é AppointmentsRepository
import AppointmentsRepository from '../repositories/AppointmentsRepository';

// Quando estamos recebendo dados do service, chamamos de Request
interface Request {
	provider: string;
  date: Date;
}

class CreateAppointmentService {
	
// Vou receber o meu repositório como uma variável do meu constructor
  constructor(appointmentsRepository: AppointmentsRepository) {
		this.appointmentsRepository = appointmentsRepository;
  }
// Todo service tem um único método dentro dele, chamado execute ou run
// É uma forma de dizer que está executando a criação de um novo appointment
  public execute() {
	  const appointmentDate = startOfHour(date);

    const findAppointmentInSameDate = this.appointmentsRepository.findByDate(
      appointmentDate,
    );
// O service não tem acesso direto aos dados da request e response, então quando ele chegar 
// em um erro, iremos realizar um throw
    if (findAppointmentInSameDate) {
      throw Error('This appointment is already booked.');
    }

    const appointment = this.appointmentsRepository.create({
      provider,
      date: appointmentDate,
    });

    return appointment;
	}
}
```

- Para que tudo funcione, precisamos mudar algumas coisas na rota também:

```tsx
import { Router } from 'express';
import { parseISO } from 'date-fns';

import AppointmentsRepository from '../repositories/AppointmentsRepository';
import CreateAppointmentService from '../services/CreateAppointmentService';

const appointmentsRouter = Router();
const appointmentsRepository = new AppointmentsRepository();

appointmentsRouter.get('/', (request, response) => {
  const appointments = appointmentsRepository.all();

  return response.json(appointments);
});

appointmentsRouter.post('/', (request, response) => {
// Vai tratar a requisição e resposta caso dê algum erro, e enviar para o
// throw no service
  try {
    const { provider, date } = request.body;

    const parsedDate = parseISO(date);

    const createAppointment = new CreateAppointmentService(
      appointmentsRepository,
    );

    const appointment = createAppointment.execute({
      date: parsedDate,
      provider,
    });

    return response.json(appointment);
  } catch (err) {
    return response.status(400).json({ error: err.message });
  }
});

export default appointmentsRouter;
```
