# insomniac-API

Este bot tem como objetivo impedir que a API entre em modo de hibernação. Ele envia requisições automáticas a cada 5 minutos, somente no período das 06:00 às 18:00 (horário comercial), garantindo que a API permaneça ativa e pronta para uso. Fora desse intervalo, o bot permanece inativo para evitar consumo desnecessário de recursos.

## Como Funciona

O projeto utiliza **GitHub Actions** para automatizar o processo de ping na API. O workflow é executado nos seguintes horários:

- **Dias**: Segunda a Sábado
- **Horário**: 06:00 às 18:00 (America/Manaus)
- **Frequência**: A cada 5 minutos

## Funcionalidades

- ✅ Ping automático na API a cada 5 minutos
- ✅ Funciona apenas em horário comercial (06:00-18:00)
- ✅ Desativa automaticamente fora do horário de trabalho
- ✅ Monitoramento de resposta HTTP
- ✅ Logging de execuções
- ✅ Possibilidade de execução manual

## API Monitorada

- **URL**: `https://my-api.example.com/`
- **Verificação**: Status HTTP 200

## Monitoramento

Você pode acompanhar as execuções do workflow na aba **Actions** do repositório GitHub. Cada execução registra:
- Horário de execução
- Código de resposta da API
- Status de sucesso/erro

## Execução Manual

O workflow também pode ser executado manualmente através da interface do GitHub Actions, útil para testes ou verificações pontuais.
