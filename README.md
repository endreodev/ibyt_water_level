# Sistema de Rede Mesh LoRa IBYT

## Visão Geral

Este projeto implementa uma rede mesh LoRa para monitoramento de níveis em reservatórios, composta por medidores e um gateway que encaminha os dados para um servidor IBYT. A arquitetura mesh permite a transmissão de dados mesmo em áreas com cobertura limitada.

## Componentes do Sistema

O sistema é composto por:
- **Medidores LoRa**: Coletam dados e formam a rede mesh
- **Gateway LoRa**: Ponte entre a rede LoRa e a internet
- **Servidor IBYT**: Recebe e processa os dados via API

## Documentação

- [Arquitetura da Rede Mesh](./docs/Rede_Mesh_Arquitetura.md) - Visão geral da arquitetura do sistema
- [Gateway LoRa](./docs/Gateway_Explicacao.md) - Documentação técnica do gateway
- [Medidor LoRa Mesh](./docs/Medidor_Explicacao.md) - Documentação técnica do medidor

## Requisitos de Hardware

### Gateway
- ESP32
- Módulo LoRa (SX1276/SX1278)
- Antena para LoRa
- Fonte de alimentação

### Medidor
- ESP32
- Módulo LoRa (SX1276/SX1278)
- Sensor de nível (conforme necessidade)
- Fonte de alimentação/bateria

## Configuração e Instalação

### Preparação do Ambiente

1. Instale o Arduino IDE (https://www.arduino.cc/en/software)
2. Adicione o suporte para ESP32:
   - Abra o Arduino IDE
   - Vá para Arquivo > Preferências
   - Adicione `https://dl.espressif.com/dl/package_esp32_index.json` ao campo "URLs Adicionais para Gerenciadores de Placas"
   - Vá para Ferramentas > Placa > Gerenciador de Placas
   - Pesquise por "ESP32" e instale

3. Instale as bibliotecas necessárias (Sketch > Incluir Biblioteca > Gerenciar Bibliotecas):
   - LoRa (por Sandeep Mistry)
   - ESP32 Web Server
   - DNSServer
   - EEPROM

### Carregando o Firmware

#### Gateway
1. Abra o arquivo `LoRa_Gateway_Config.ino` no Arduino IDE
2. Selecione a placa ESP32 correta em Ferramentas > Placa
3. Conecte o ESP32 via USB
4. Selecione a porta correta em Ferramentas > Porta
5. Clique em Carregar (seta para a direita)

#### Medidor
1. Abra o arquivo `Medidor_Mesh.ino` no Arduino IDE
2. Siga os mesmos passos 2-5 acima

## Conexões de Hardware

### Gateway e Medidor (pinos LoRa)
```
ESP32    | Módulo LoRa
---------|-----------
GPIO5    | SCK
GPIO19   | MISO
GPIO27   | MOSI
GPIO18   | SS (NSS)
GPIO14   | RST
GPIO26   | DIO0
```

### Sensor de Nível (Medidor)
Conecte o sensor de nível conforme a especificação do sensor utilizado. O código atual simula leituras de nível - substitua a função `lerNivel()` pela implementação específica do seu sensor.

## Configuração Inicial

### Gateway
1. Ao ligar pela primeira vez, o gateway criará uma rede WiFi chamada "LoRa_Gateway_Config"
2. Conecte-se a esta rede com qualquer dispositivo com WiFi
3. Abra um navegador e acesse http://192.168.4.1
4. Faça login com a senha padrão: `#1@2#3$4%5`
5. Configure:
   - Rede WiFi (SSID e senha)
   - URL da API (padrão: https://ibyt.com.br/api.php/)
   - Senha de administrador
   - Dispositivos autorizados (opcional)

### Medidor
1. Ao ligar pela primeira vez, o medidor criará uma rede WiFi chamada "Medidor_LoRa_Config"
2. Conecte-se a esta rede com qualquer dispositivo com WiFi
3. Abra um navegador e acesse http://192.168.4.1
4. Faça login com a senha padrão: `#1@2#3$4%5`
5. Configure:
   - Rede WiFi (SSID e senha)
   - Código do cliente (C)
   - Código do reservatório (R)
   - URL do endpoint
   - ID do recipiente (gateway ou outro medidor próximo)

## Funcionamento

Após a configuração:

1. Os medidores coletam dados periodicamente (a cada 60 segundos)
2. Os dados são formatados como `C2R1N50` (exemplo)
3. Os medidores tentam enviar os dados para o gateway
4. Se não conseguirem, tentam enviar para outro medidor (mesh)
5. O gateway recebe os dados e encaminha para a API configurada
6. Apenas mensagens com o prefixo "IBYT:" são aceitas pelo gateway

## Segurança

- Interface web protegida por senha
- Filtragem de dispositivos não autorizados
- Prefixo "IBYT:" obrigatório nas mensagens
- Verificação opcional de IDs específicos

## Solução de Problemas

- **Dispositivo não inicia modo AP**: Pressione o botão reset
- **Erro ao conectar WiFi**: Verifique as credenciais na configuração
- **Não recebe dados LoRa**: Verifique as conexões dos pinos e a distância entre dispositivos
- **Gateway não envia para API**: Verifique a conectividade WiFi e a URL configurada

## Contribuição

Para contribuir com este projeto:
1. Faça um fork do repositório
2. Crie uma branch para sua feature (`git checkout -b feature/nova-funcionalidade`)
3. Commit suas mudanças (`git commit -m 'Adiciona nova funcionalidade'`)
4. Push para a branch (`git push origin feature/nova-funcionalidade`)
5. Abra um Pull Request

## Licença

Este projeto está licenciado sob a licença MIT - veja o arquivo LICENSE para detalhes.
