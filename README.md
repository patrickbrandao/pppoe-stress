# pppoe-stress

Ferramentas para teste de desempenho de servidores PPPoE

<pre>

O protocolo PPPoE e' composto pelos seguintes protocolos (ethertype - camada 2):
0x8863	PPPoE Discovery Stage, PPPOED
0x8864	PPPoE Session Stage, PPPOES

O Protocolo PPPOED e' responsavel pela descoberta, inicializacao e finalizacao
do tunel MAC a MAC.

Estagio 1 - PPPOED - para o cliente descobrir o servidor pppoe: PADI
Estagio 2 - PPPOED - para o servidor responder ao cliente se oferecendo: PADO
Estagio 3 - PPPOED - para o cliente requisitar uma nova sessao: PADR
Estagio 4 - PPPOED - para o servidor responder ao cliente e determinar o id da sessao: PADS

Estagio 5 - PPPOES - estabelecido o tunel mac a mac usando um id de sessao unico
                    A fase de negociacao do protocolo PPP se inicia.
                    Se a negociacao PPP falhar, o PPPOED-PADT e' enviado pela ponta
                    que recusou o parametro inegociavel.
                    Se a negociacao for bem sucedida, os sub-protocolos para obter
                    ipv4 e ipv6 sao iniciados.
                    
                    Durante a sessao, as pontas enviam PPP-LCP-echo-request e esperam
                    por uma resposta PPP-LCP-echo-reply, a ausencia de respostas
                    echo-reply podem fazer com que a ponta sem respostas envie um
                    PPPOED-PATD e abandone a sessao.

Estagio 6 - PPPOED - para qualquer um dos lados requisitar o fim da sessao: PADT, nao ha concentimento
                    da outra ponta do tunel para que um dos lados envie um PADT e abadone a sessao.
                    Se a outra ponta ignorar ou nao receber o PADT e tentar participar da sessao o
                    servidor nao respondera nada.

Testes de stress:

* pppoe-padi-scan
    Disparar flood de PADI broadcast (dst-mac ff:ff:ff:ff:ff:ff ou unicast (para o mac do pppoe-server).
    -b - envia para broadcast
    -d - envia para o mac de destino
    -c N - envia N quadros sem processar respostas
    -k - modo KILL, envia infinitamente, silenciosamente.
    -i - obtem informacao sobre os servidores PPPoE disponiveis na rede (modo descoberta)

* pppoe-padr-stress
  Dispara ataque para alocar numero de sessao (resulta em negacao de servico)
  (em analise de cookies e modo promiscuo)
  
* pppoe-parallels
  Cria conexoes paralelas para testar capacidade de usuarios do servidor (rodar no cliente),
  Testa:
    Capacidade de recuperacao (quando todos desconectam e reconectam ao mesmo tempo),
    Capacidade de autenticar (tambem estressa o servidor RADIUS),

  Parametros:
  -u FILE - arquivo com lista de nome de usuarios e senhas (formato csv, usuario;senha, um por linha)
  -c N - numero de conexoes
  -x SCRIPT - caminho do script para tomar acoes posteriores a conexao estabelecida, recebe parametros:
      1 - ip do lado servidor
      2 - ip do lado cliente (meu ip)
      3 - mac do lado servidor
      4 - mac do lado cliente (meu mac)
      5 - id da sessao em hexadecimal
      6 - nome do profile, se a lista de usuarios possuir um terceiro valor,
          esse valor e' considerado o nome de profile, normalmente utilizo
          isso para iniciar outros scripts e simular o perfil de navegacao
          do usuario (fazer downloads, ataques tcp, ataques udp e dns), pings, etc...
      
      Exemplo:
      ./SCRIPT ppp0 1.2.3.4 200.1.2.3 00:aa:bb:11:22:33 00:ff:fe:fd:fc:db 0xa9 cliente_normal

  -t N - finaliza a sessao em N segundos (padrao 0 = sem max-session-time)
  -k - modo kill, derruba a conexao sempre que ela for bem sucedida, resulta em
      stress do servidor PPPoE e o servidor RADIUS

* pppoe-view
  Monitorar de quadros
  Parametros:
  (if) - interface de rede a monitorar (eth, vlan, eoip, vpls, gre, ...)
  -d - apenas PPPoE Discovery
  -s - apenas PPPoE Session
  -1 - apenas PPPoE Discovery tipo PADI
  -2 - apenas PPPoE Discovery tipo PADO
  -3 - apenas PPPoE Discovery tipo PADR
  -4 - apenas PPPoE Discovery tipo PADS
  -5 - apenas PPPoE Session (apelido de -s)
  -6 - apenas PPPoE Discovery tipo PADT
  -a - apenas PPPoE Session - PPP - LCP
  -b - apenas PPPoE Session - PPP - LCP-echo-request
  -c - apenas PPPoE Session - PPP - LCP-echo-reply
  -d - apenas PPPoE Session - PPP - negociacao IPv4
  -e - apenas PPPoE Session - PPP - negociacao IPv6 ND
  -f - apenas PPPoE Session - PPP - negociacao IPv6 PD

  Exemplo:
    pppoe-view eth0 -1
  


</pre>
