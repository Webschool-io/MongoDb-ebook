## MongoDb Cliente

### mongo-hacker

O [mongo-hacker](https://github.com/TylerBrock/mongo-hacker) é uma extensão para seu terminal que adiciona algumas funcionalidades a mais, como por exemplo *syntax highlighter*, vou mostrar como é no meu terminal:

![Visualização do terminal rodando o mongo-hacker](https://github.com/Webschool-io/be-mean-instagram/raw/master/Apostila/module-mongodb/images/mongo-hacker-terminal.png)

## Instalação#


### Linux

Intalando o git. no ubuntu e Debian, funciona também nos derivados.

```
sudo apt-get install git -y

```

Intalando o git. no fedora 23

```
sudo dnf install git -y

```

Clonando o projeto mongo-hacker que pertence TylerBlock.


```
git clone https://github.com/TylerBrock/mongo-hacker

```

Algumas vezes no distro que está utilizando (Sistema Operacional) não vem instalado o gcc e g++ que são dependencias necessarias na instalação, vamos instalar.

No Ubuntu / Debian  (e derivados).

```
sudo apt-get install gcc g++ -y

```

No Fedora 23.

```
sudo dnf install gcc g++ -y

```

Após a instalação do gcc e g++ vamos instalar o mongo-hacker.

```
cd mongo-hacker/
sudo make install

```

### Database

Para listarmos nossas *databases* precisamos apenas executar o seguinte comando no cliente do MongoDb:

```
show dbs
local  0.0GB
```

E aparecerá a listagem das *databases* existentes.

Em versões anteriores a atual(3.2.1) a storage engine layer do mongoDB pré alocava  `0.078GB` , o que dá algo em torno de 80MBs, ela fazia isso para melhorar a perfomance na hora da busca e sempre garantir um espaço sequencial para a persistência.

Após a compra da Wired Trigger(WT) o MongoDB começou a evoluir na sua Storage Engine Layer melhorando o uso de espaço em disco e a performance.

Sendo assim não se faz mais necessario a pré-alocação dos dados.

Agora vamos criar o nosso banco para iniciar o nosso Instagram, execute o seguinte comando:

```
use be-mean-instagram
switched to db be-mean-instagram
```

Onde `be-mean-instagram` é o nome do nosso banco e ele está referenciado na variável `db`, então se quiser ver qual banco de dados estamos usando basta você executar `db` no cliente:

```
db
be-mean-instagram
```

perceba que o banco `database-test` que possuo já possui `0.078GB` de tamanho, porém não contém **nenhum** dado.

#### dropDatabase

Para apagarmos um banco de dados é bem simples, basta execurtarmos a função `dropDatabase()` após termos definido nosso banco com `use nome_do_banco`:

```js
use banco_a_remover
switched to db banco_a_remover

db.dropDatabase()

{
  "dropped": "banco_a_remover",
  "ok": 1
}

```

##### CUIDADO

> Esse comando precisa de um *lock* de escrita **global** e irá bloquear outras operações até estar completa.

### Collection

### Import

### Export

### Replica

### Sharding

### GridFs
