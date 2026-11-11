# Registo de Horas · joaogoncalves.net

App estática para registar as horas da Laura e controlar o pagamento mensal.
GitHub Pages + JSONBin.io, sem servidor.

**Endereço:** https://joaogoncalves.github.io/registo-horas/

## Acessos

| Quem | Como entra | O que pode fazer |
|---|---|---|
| **Laura** | PIN no teclado numérico | Registar/corrigir horas do mês (até estar pago), deixar observações para o João e a Elsa, enviar mensagens soltas, registar despesas extra, ler notas e marcar como lidas, ver total a receber |
| **João / Elsa** | "Entrar como administrador" + password de cada um | Tudo: valor-hora com histórico, notas, editar/apagar registos, marcar mês como pago, mensagem de WhatsApp para a Laura, exportar Excel, imprimir PDF |

Cada nota, registo manual e pagamento fica com o nome de quem o fez.
A sessão fica guardada no dispositivo durante 60 dias (`CONFIG.sessaoDias`).

## Segurança — sem segredos no código

O `index.html` **não contém** o PIN, as passwords nem a chave do JSONBin.
Em `CONFIG.acessos` há apenas texto encriptado: o Bin ID e a chave do JSONBin
encriptados (AES-GCM 256, chave derivada com PBKDF2-SHA256, 150 000 iterações)
uma vez com o PIN da Laura e uma vez com a password de cada administrador.
Ao entrar, a app tenta desencriptar com o que foi escrito: se conseguir, liga-se ao JSONBin.

Limitação honesta: um PIN de 4 algarismos pode ser descoberto por alguém com
conhecimentos técnicos que teste as 10 000 combinações. Para dados de horas e
notas é proteção adequada; usa um PIN de 6 algarismos se quiseres mais margem.

## Mudar PIN, passwords ou chave

1. Abre `gerar-acessos.html` (no site ou no ficheiro local — funciona offline).
2. Preenche Bin ID, Access Key, PIN e passwords → **Gerar**.
3. Substitui a lista `acessos: [ … ]` no `index.html` pelo resultado e faz commit.
4. Se mudares o número de algarismos do PIN, atualiza `CONFIG.pinDigitos`.

## JSONBin

- Bin privado com o conteúdo inicial:
  ```json
  {"version":1,"rates":[],"registos":[],"despesas":[],"pagamentos":{},"notas":[],"updatedAt":null}
  ```
- Access Key só com permissões **Bins: Read + Update**.

## Instalar no telemóvel

Abre o link → *Partilhar → Adicionar ao ecrã principal* (iPhone) ou *⋮ → Instalar app* (Android).

## Identidade visual

Extraída do cartão de visita:

- **Cores:** carvão `#231f20`, verde-lima `#87cf32`, branco (`#3f7310` para texto verde sobre branco).
- **Logótipo:** `logo.svg` (fundo escuro) e `logo-escuro.svg` (fundo claro), vetoriais.
- **Fonte da marca:** FFF Corporate — coloca `fonts/fff-corporate.woff2` para a ativar; sem ela usa Chakra Petch + Inter.

## Regras

- **Valor-hora com histórico:** cada registo usa o valor em vigor na sua data. Por defeito a data é hoje, mas pode ser anterior.
- **Pagamento:** ao marcar o mês como pago, valor e horas ficam congelados; a Laura deixa de poder alterar esse mês.
- **Despesas extra** somam-se ao total do mês.
- **Novidades da Laura:** observações, despesas e mensagens novas aparecem no topo da vista de administração até "Visto ✓". A Laura vê se a mensagem já foi vista e por quem.
- **Notas** "para a próxima limpeza" ficam em destaque até a Laura carregar em "Li ✓".
- Antes de gravar, a app lê sempre a versão mais recente, para não apagar alterações feitas noutro telemóvel.
- **Modo demo:** se `acessos` estiver vazio, os dados ficam só no browser (PIN `1234`, password `demo`).
