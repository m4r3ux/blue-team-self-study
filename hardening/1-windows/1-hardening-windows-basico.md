## Least Previlege
A primeira coisa, é usar o Least Previlege e em um disco local do PC, por exemplo - modificar as permissões do que o usuário ou grpo de usuários pode acessar.

Vamos supor que temos a task de criar um grupo chamado "Estagiários", esse grupo não precisa ter permissão de Controle total á máquina, mas apenas os necessários.

## Atualizações 
Sempre se certificar de que o host esteja com o sistema atualizado.

## Desativar recursos sem sentido
Indo em Painel de Controle > Programas > Ativar ou desativar recursos do Windows - podemos desativar coisas que não fazem sentido, como o compartilhamento de arquivos via SMB.

Também sobre programas, tentar não deixar o sistema lotado de programas que nunca serão usados - é um ótimo caminho pra malware, problemas de segurança e também de desempenho.

## Windows Security

### Controle baseado de aplicativo e navegador
Podemos ativar tudo, como bloquear app baseado em reputação, ou app possivelmente indesejado.

### Segurança do dispositivo
Podemos ir Isolamento do núcleo e ativar "Integridade da memória", que evita ataques de injeção de código malicioso em processos.

## AutoPlay
É um feature de reprodução automática para mídias e dispositivos conectados no computador - altamente inseguro, pois, um USB infectado conectado na máquina poderia ser fatal.

## Bluetooth
Agora outra opção a ser desativada, é em Configurações de Bluetooth, desativando a descoberta desse PC via bluetooth - tornando-o escondido para dispositivos procurando por vitimas.