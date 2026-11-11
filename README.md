# Registo de Horas · joaogoncalves.net

App estática (um único `index.html`) para registar as horas da Laura e controlar o pagamento mensal.
Segue a mesma abordagem do projeto Sociedade Euromilhões: GitHub Pages + JSONBin.io + login front-end.

## Acessos

| Quem | Como entra | O que pode fazer |
|---|---|---|
| **Laura** | PIN no teclado numérico | Registar/corrigir horas do mês (até estar pago), deixar observações para o João, registar despesas extra, ler notas e marcar como lidas, ver total a receber |
| **João / Elsa (admin)** | "Entrar como administrador" + password de cada um | Tudo: valor-hora com histórico, notas, editar/apagar registos, marcar mês como pago, exportar Excel, copiar resumo/WhatsApp, imprimir PDF |

Cada nota, registo manual e pagamento fica com o nome de quem o fez — a Laura vê se a nota veio do João ou da Elsa.

A sessão fica guardada no dispositivo durante 60 dias (`CONFIG.sessaoDias`).

## Configuração (topo do `<script>` em `index.html`)

```js
const CONFIG = {
  nomeColaboradora: "Laura",
  admins: [
    { nome: "João", password: "mudar-password-joao" },
    { nome: "Elsa", password: "mudar-password-elsa" }
  ],
  pinColaboradora:  "1234",
  telefoneColaboradora: "351912345678",   // WhatsApp da Laura (opcional)
  jsonbin: { binId: "", key: "", keyHeader: "X-Access-Key" }
};
```

### 1. JSONBin (dados partilhados entre os dois telemóveis)

1. Em <https://jsonbin.io> → **Bins** → *Create Bin* com o conteúdo:
   ```json
   {"version":1,"rates":[],"registos":[],"pagamentos":{},"notas":[],"updatedAt":null}
   ```
   Deixa o bin **privado**. Copia o **Bin ID**.
2. Em **API Keys → Access Keys** cria uma chave só com permissões de **Bins: Read + Update**.
   Cola-a em `key` (mantém `keyHeader: "X-Access-Key"`).
   > Evita usar a X-Master-Key: num repositório público dá acesso a **todos** os teus bins. Se a usares, muda `keyHeader` para `"X-Master-Key"`.
3. Sem `binId`/`key`, a app funciona em **modo local** (dados só nesse browser) — útil para testar.

### 2. GitHub Pages

1. Cria um repositório (ex.: `registo-horas`) e envia estes ficheiros.
2. *Settings → Pages → Deploy from branch → main / root*.
3. Abre `https://<utilizador>.github.io/registo-horas/`.

### 3. Telemóvel da Laura

Abre o link → *Partilhar → Adicionar ao ecrã principal* (iPhone) ou *⋮ → Instalar app* (Android). Fica com ícone próprio.

## Identidade visual

Extraída do cartão de visita:

- **Cores:** carvão `#231f20`, verde-lima `#87cf32`, branco. Para texto verde sobre fundo branco usa-se `#3f7310`, por legibilidade.
- **Logótipo:** `logo.svg` (versão para fundo escuro, vetorial, tirada do cartão) e `logo-escuro.svg` (para fundo claro).
- **Fonte da marca:** FFF Corporate (pixel). Para a usar na web, converte o teu ficheiro da fonte para `fonts/fff-corporate.woff2`. Sem esse ficheiro, a app usa Chakra Petch (Google Fonts) nos títulos e Inter no texto.
- Todas as cores e fontes estão em variáveis CSS no início do `<style>`.

## Regras importantes

- **Valor-hora com histórico:** cada registo usa o valor em vigor na data do registo. Ao marcar um mês como pago, o valor e as horas ficam congelados nesse pagamento.
- **Meses pagos ficam bloqueados para a Laura.** O admin pode corrigir (ou "Anular pagamento").
- **Despesas extra** registadas pela Laura somam-se ao total do mês. As observações e despesas novas aparecem em "Novidades da Laura" até carregares em "Visto ✓".
- **Mensagem de pagamento:** depois de marcar o mês como pago, o botão "Enviar mensagem à Laura" abre o WhatsApp com o texto pronto (depois envias o comprovativo).
- **Um registo por dia** (qualquer dia da semana, não só segunda-feira).
- Notas "para a próxima limpeza" aparecem em destaque até a Laura carregar em **Li ✓**. Se editares uma nota, volta a ficar por ler.
- Antes de gravar, a app lê sempre a versão mais recente na nuvem, para não apagar alterações feitas no outro telemóvel.

## Segurança

Tal como no Euromilhões, o login é só front-end: o PIN, a password e a chave JSONBin ficam visíveis no código-fonte. Para este uso chega, mas não é proteção a sério.
