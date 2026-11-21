# Painel 308 — painel simples para Peugeot 308 (ELM327 Bluetooth)

Uma tela só, em modo paisagem, com:

- Velocidade (km/h) e RPM
- Temperatura do motor (°C)
- Ajuste de combustível de curto e longo prazo (STFT/LTFT, banco 1, em %)
- km/l instantâneo e km/l médio da viagem (gasolina ou etanol)

Também funciona como launcher (tela inicial) e tem o botão **Apps** para abrir rádio, música etc.

> **Importante:** este projeto foi escrito sem Android Studio, sem o Android SDK e sem acesso a um
> carro, então **não foi compilado nem testado**. Pode haver um erro de compilação simples para
> corrigir na primeira vez. O APK precisa ser gerado por você (passos abaixo).

## 1. Gerar o APK

1. Instale o Android Studio (versão Koala ou mais nova).
2. `File > Open` e escolha a pasta `Painel308`. Aguarde o Gradle sincronizar (precisa de internet nessa etapa).
3. Menu `Build > Build Bundle(s) / APK(s) > Build APK(s)`.
4. Quando terminar, clique em **locate**. O arquivo é `app/build/outputs/apk/debug/app-debug.apk`.

## 2. Instalar na multimídia por pendrive

1. Copie `app-debug.apk` para um pendrive (FAT32).
2. Espetre na multimídia e abra pelo gerenciador de arquivos.
3. Se pedir, permita "instalar apps de fontes desconhecidas".
4. Abra o app, aceite as permissões de Bluetooth e notificações.

## 3. Usar como tela inicial

Aperte o botão Home da multimídia. Se o Android perguntar, escolha **Painel 308** e **Sempre**.
Se não perguntar, vá em `Configurações > Apps > Aplicativo de início` (o nome varia) e escolha Painel 308.
Para voltar ao launcher original, troque nessa mesma configuração.

## 4. Configurar o OBD2 Bluetooth

1. Plugue o ELM327 na porta OBD2 do carro e ligue a ignição.
2. Nas configurações de Bluetooth da multimídia, procure e pareie o adaptador (senha comum: `1234` ou `0000`).
3. No Painel 308, toque na engrenagem (⚙), escolha o adaptador na lista e o combustível.
4. A conexão é automática. Se cair, o app tenta de novo a cada 3 segundos.

## 5. Como ler os números

- **Ajuste de combustível:** perto de 0% é o ideal. Verde até ±10%, amarelo até ±15%, vermelho acima.
  Positivo significa que a ECU está colocando combustível a mais (mistura pobre); negativo, a menos.
  O longo prazo é o mais importante para saber se há problema (vazamento de ar, bico, sensor).
- **Temperatura:** azul = motor frio, verde = normal, amarelo/vermelho = quente demais.
- **km/l:** a tela sempre diz a origem do cálculo na linha de baixo.
  - *medido pela ECU*: só se o carro informar a taxa de consumo (PID 5E).
  - *calculado (MAF)*: usa o fluxo de ar medido e assume mistura ideal.
  - *ESTIMADO (MAP)*: usado quando a ECU não tem MAF; é aproximado. Se o seu motor cair nesse caso, a linha fica amarela.
- **Calibração:** abasteça até completar o tanque, rode, abasteça de novo e compare o km/l real com o mostrado.
  Novo fator = fator atual × (km/l mostrado ÷ km/l real). Ajuste no controle deslizante em ⚙.

## Limitações conhecidas

- A ECU do 308 pode não expor todos os PIDs; o que não responder aparece como `--`.
- Clones baratos de ELM327 variam bastante; alguns são lentos (a atualização fica em poucos Hz).
- No Android 14+, o serviço em segundo plano exige a permissão de Bluetooth concedida.
- O início automático ao ligar depende de a multimídia enviar o aviso de boot; sendo o launcher, o app abre sozinho ao ligar.

## Arquivos

- `MainActivity.kt` — tela cheia, permissões, inicia o serviço
- `ObdService.kt` — conexão Bluetooth, leitura dos PIDs, cálculo da viagem
- `Elm327.kt` — comandos AT e leitura de PIDs
- `FuelCalc.kt` — fórmulas de consumo
- `DashboardScreen.kt` — a interface
- `Prefs.kt`, `ObdRepo.kt`, `BootReceiver.kt` — apoio
