Instituto Superior Técnico, Universidade de Lisboa

**Segurança Informática em Redes e Sistemas**

# Guia de Configuração - *Kathará*

## Objectivo

O objectivo deste guia é apresentar o *[Kathará](https://www.kathara.org/)*, descrever como se instala e verificar o seu funcionamento básico.

## O que é o Kathará

O *Kathará* é uma ferramenta que permite simular redes de computadores e que será usada como base para os laboratórios e o projeto.
Vamos utilizar o Kathará para simular redes e comparar protocolos e configurações alternativas.

Em poucas palavras, o Kathará permite criar várias máquinas - que são concretizadas por contentores  de *[Docker](https://docs.docker.com/)* - e escolher como estão ligadas em domínios de colisão (*collision domains*), isto é, pontos da rede simulada em que os pacotes chegam a todos os computadores por difusão (*broadcast*), e que são concretizados por *[Docker Bridge Networks](https://docs.docker.com/network/bridge/)*.

Este vídeo faz uma apresentação do Kathará:

<https://www.youtube.com/watch?v=ionEpKjv3Vk>

## Instalação do Kathará

O Kathará está instalado nos computadores do laboratório, em Ubuntu GNU/Linux.
Para aceder aos computadores do laboratório use o *username* "seed" e a *password* "dees".

Para instalar o Kathará no seu PC, faca o *download* da versão *"Latest Stable Release"* de

> <https://www.kathara.org/#download>

e siga os passos indicados em

> https://github.com/KatharaFramework/Kathara/wiki

## Teste básico

O objetivo deste teste básico e verificar que o Kathará está corretamente instalado.
Vamos criar uma rede em que temos 2 computadores fisicamente ligados ao mesmo *switch* e confirmar que se conseguem pingar mutuamente.

1. Clone este repositório git para a sua máquina

```bash
git clone 'git@github.com:tecnico-sec/Kathara-Setup.git'
````

2.  Observe o conteúdo do ficheiro *lab.conf* de modo a compreender porque é gerada a rede entre os 2 pcs.

3.  Execute o laboratório mudando para o directório que contém o ficheiro *lab.conf* e executando o comando `lstart` (`kathara lstart`). 
Observe o arranque dos dois docker containers.
Verifique que os scripts bash pc1.startup e pc2.startup são executado após o arranque das respetivas máquinas.

4.  Em cada máquina, obtenha o seu endereço ip dentro da rede 1.2.0.0/24.

```bash
ip addr
```

| Máquina  | IP address    |
| -------- | ------------- |
| pc1      |               |
| pc2      |               |


5. Pingue as maquinas mutuamente.

```bash
# iex: no pc1
ping 1.2.0.2
# ctrl+c para parar o comando 
```

6. Feche o laboratório, executando o comando `lclean` (`kathara lclean`).
Isto vai apagar o conteúdo das máquinas e parar de corre-las.

# Guardar e observar o tráfego entre máquinas

Vamos agora proceder a guardar o tráfego de um ping entre máquinas do ponto de vista dos pacotes que chegam a interface de rede de cada máquina e posteriormente observa-lo no host pc.

1. Na pasta onde esta o ficheiro lab.conf, a pasta shared e uma pasta persistente partilhada entre todas as maquinas e o host pc. Vamos utilizá-lo para dumpar o trafego de rede que chega a interface de rede de cada pc. Para o fazer, vamos começar por criar essa pasta no host pc.

```bash
# Dentro da pasta que contem lab.conf: 
mkdir shared
```

2. Inicie o laboratório:
```bash
lstart
```

3. Execute o comando [tcpdump][7] nas máquinas respetivas para começar a guardar o trafego que chega a interface eth0 dentro dos ficheiros respetivos na pasta shared:
```bash
# pc1:
tcpdump -s 0 -i eth0 -w /shared/pc1.pcap &

# pc2:
tcpdump -s 0 -i eth0 -w /shared/pc2.pcap &

```

4. Na maquina pc1 pingue a maquina pc2 e de seguida pingue um endereço que não existe:
```bash
# pc1:
ping -c 1 1.2.0.2
ping -c 1 1.2.0.3
```

5. Pare os laboratórios, executando no host pc `lclean`.

6. Corra o programa [wireshark][8] e abra um dos pacotes para observar o conteúdo do ping entre o pc1 e pc2 do ponto de vista dos pacotes que chegam ao pc1 e ao pc2 quando estes estão ligados no mesmo collision domain do Kathará. Que componente físico de rede teria o mesmo comportamento que o Kathará simula num collision domain?


## Referências

-   Kathará, [https://github.com/KatharaFramework/Kathara/wiki/][3]

-   Docker documentation, [https://docs.docker.com/][6]

-   man ip

-   man tcpdump, [https://www.tcpdump.org/manpages/tcpdump.1.html][7]

-  Wireshark, [https://www.wireshark.org/][8]

  [3]: https://github.com/KatharaFramework/Kathara/wiki
  [6]: https://docs.docker.com/
  [7]: https://www.tcpdump.org/manpages/tcpdump.1.html
  [8]: https://www.wireshark.org/