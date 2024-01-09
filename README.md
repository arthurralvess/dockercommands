# dockercommands

Docker e Imagens

- docker images  #listarmos as imagens que nós temos no nosso host
- docker pull (parametro)  #baixar imagem para o nosso host
- docker run hello-world  #criar um contêiner
- docker ps  # verificar o status do nosso contêiner
- docker ps -a #verificar o status de todos nosso contêiners
- docker stats (id ou apelido do container)  # Para que possamos ter informações sobre um contêiner
- docker inspect (id da imagem ou container)  #retorna um json com todas as informações relacionadas a nossa busca
- docker rm  #deletar alguma imagem
- docker exec (id_container ou nome_container)  #podemos executar qualquer comando nos nossos contêineres
                                                    - -i permite interagir com o container
                                                    -t associa o seu terminal ao terminal do container
                                                    -it é apenas uma forma reduzida de escrever -i -t
                                                    --name algum-nome permite atribuir um nome ao container em execução
                                                    -p 8080:80 mapeia a porta 80 do container para a porta 8080 do host
                                                    -d executa o container em background
                                                    -v /pasta/host:/pasta/container cria um volume '/pasta/container' dentro do container com o conteúdo da pasta '/pasta/host' do host
- docker attach (id_container ou nome_container) #entrar dentro do nosso contêiner
- docker start (id do container)  #subir ele novamente vamos utilizar o comando start.
- docker stop (id ou nome container) #parar um contêiner que esteja sendo executado
- docker run -it -d -p 8080:80 nginx  #baixar a imagem do nginx para o seu host e abrir ele na porta 8080 do seu host. http://localhost:8080/


Docker e Volume 

- docker volume create  #Usado para criar um novo volume Docker.
- docker volume list  #Usado para listar todos os volumes Docker existentes.
- docker volume rm  #Usado para excluir um volume Docker.
- docker volume inspect [nome_do_volume]  # Inspecionando um volume
- https://www.linkedin.com/pulse/persistência-de-dados-em-um-docker-container-ailton-cordeiro/?originalSubdomain=pt


DockerFile

Exemplo:

FROM python:3.9.0-alpine                                                                    #imagem base
LABEL maintainer "Geek University <contato@geekuniversity.com.br>"
COPY . /var/www                                                                                 #Copiar arquivos do projeto
WORKDIR /var/www                                                                             #diretório de trabalho
RUN apk update                                                                                  # Instalar dependências
EXPOSE 8000                                                                                      # Expor a porta 8000
CMD ["npm", "start"]                                                                           #Iniciar Aplicação

Execute: - docker build -t Dockerfile



Docker e Redes 

- docker network ls  #lista as redes
- bridge, none e host




Docker Compose

https://blog.4linux.com.br/docker-compose-explicado/

Docker Compose é utilizado justamente para facilitar o provisionamento e gerenciamento de multi-contêineres principalmente em ambientes de desenvolvimento, testes automatizados ou cenários de execução em um único host, bastando apenas um arquivo YAML com as instruções e parâmetros desejados para os nossos contêineres e que com um único comando conseguimos realizar a execução e/ou atualização de todos eles. Ele ainda realiza o isolamento do ambiente de um conjunto de contêineres separando-os por nome de “projeto”: por padrão é dado ao projeto o nome do diretório onde este está sendo executado, porém é possível atribuir um outro utilizando o parâmetro -p.

docker-compose up: cria e inicia os contêineres;
docker-compose build: realiza apenas a etapa de build das imagens que serão utilizadas;
docker-compose logs: visualiza os logs dos contêineres;
docker-compose restart: reinicia os contêineres;
docker-compose ps: lista os contêineres;
docker-compose scale: permite aumentar o número de réplicas de um contêiner;
docker-compose start: inicia os contêineres;
docker-compose stop: paralisa os contêineres;
docker-compose down: paralisa e remove todos os contêineres e seus componentes como rede, imagem e volume.


exemplo: 

version: '3.8'

networks:
  web_network:
    driver: overlay

volumes:
  site_conf:

services:
  web-server:
    image: httpd:latest
    ports: "80:80"
    deploy:
      placement:
        constraints:
          - "node.role==worker"
      mode: replicated
      replicas: 2
      resource:
        cpus: "0.50"
        memory: 256M
    restart: always
    networks:
      - web_network
    volumes:
      - /dir/site/html:/var/www/html
      - site_conf:/etc/httpd





https://stack.desenvolvedor.expert/appendix/docker/compose.html


version: '2'
services:
  web:
    build: .
      context: ./dir
      dockerfile: Dockerfile-alternate
      args:
        versao: 1
    ports:
      - "5000:5000"
  redis:
    image: redis


No arquivo acima temos a primeira linha que define a versão do docker-compose.yml, que no nosso caso usaremos a versão mais atual do momento, caso tenha interesse em saber a diferença entre as versões possíveis, veja esse link.

            version: '2'

No mesmo nível de indentação temos services, que define o início do bloco de serviços que serão definidos logo abaixo.

           version: '2'
           services:

No segundo nível de indentação (aqui feito com dois espaços) temos o nome do primeiro serviço desse arquivo, que recebe o nome de web. Ele abre o bloco de definições do serviço, ou seja, a partir do próximo nível de indentação, tudo que for definido faz parte desse serviço.

           version: '2'
           services:
             web:

No próximo nível de indentação (feito novamente com mais dois espaços) temos a primeira definição do serviço web, que nesse caso é o build que informa que esse serviço será criado não a partir de uma imagem pronta, mas que será necessário construir sua imagem antes de sua execução. Seria o equivalente ao comando docker build. Ele também abre um novo bloco de código para parametrizar o funcionamento dessa construção da imagem.

            version: '2'
            services:
             web:
               build: .

No próximo nível de indentação (feito novamente com mais dois espaços) temos um parâmetro do build, que nesse caso é o context. Ele é responsável por informar qual contexto de arquivos será usado para construir a imagem em questão, ou seja, apenas arquivos existentes dentro dessa pasta poderão ser usados na construção da imagem. O contexto escolhido foi o “./dir”, ou seja, isso indica que uma pasta chamada dir, que se encontra no mesmo nível de sistema de arquivo do docker-compose.yml ou do lugar onde esse comando será executado, será usada como contexto da criação dessa imagem. Quando logo após da chave um valor é fornecido, isso indica que nenhum bloco de código será aberto.

    build: .
      context: ./dir

No mesmo nível de indentação da definição context, ou seja, ainda dentro do bloco de definição do build, temos o dockerfile, ele indica o nome do arquivo que será usado para construção da imagem em questão. Seria o equivalente ao parâmetro “-f” do comando docker build. Caso essa definição não existisse, o docker-compose procuraria por padrão por um arquivo chamado Dockerfile dentro da pasta informada no context.

    build: .
      context: ./dir
      dockerfile: Dockerfile-alternate

No mesmo nível de indentação da definição dockerfile, ou seja, ainda dentro do bloco de definição do build, temos o args, ele define os argumentos que serão usados pelo Dockerfile, seria o equivalente ao parâmetro “–build-args” do comando docker build. Como não foi informado o seu valor na mesma linha, fica evidente que ela abre um novo bloco de código.

No próximo nível de indentação (feito novamente com mais dois espaços) temos a chave “versao” e o valor “1”, ou seja, como essa definição faz parte do bloco de código args, essa chave valor é o único argumento que será passado para o Dockerfile, ou seja, o arquivo Dockerfile em questão deverá estar preparado para receber esse argumento ou ele se perderá na construção da imagem.

    build: .
      context: ./dir
      dockerfile: Dockerfile-alternate
      args:
        versao: 1

Voltando dois níveis de indentação (quatro espaços a menos em relação a linha anterior) temos a definição ports, que seria o equivalente ao parâmetro “-p” do comando docker container run. Ele define qual porta do container será exposta no Docker host. Que no nosso caso será a porta 5000 do container, com a 5000 do *Docker host.

  web:
    build: .
    ...
    ports:
      - "5000:5000"

Voltando um nível de indentação (dois espaços a menos em relação a linha anterior) saímos do bloco de código do serviço web, isso indica que nenhuma definição informada nessa linha será aplicada a esse serviço, ou seja, precisamos iniciar um bloco de código de um serviço novo, que no nosso caso será com nome de redis.

  redis:
    image: redis

No próximo nível de indentação (feito novamente com mais dois espaços) temos a primeira definição do serviço redis, que nesse caso é o image que é responsável por informar qual imagem será usada para iniciar esse container. Essa imagem será obtida do repositório configurado no Docker host, que por padrão é o hub.docker.com.
