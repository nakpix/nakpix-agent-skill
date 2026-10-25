# NAKPIX Agent Skill

Skill oficial para **NAKPIX** — pague PIX com cripto (USDT na Polygon/Solana)
usando agentes de IA (OpenClaw, Hermes, Claude, OpenCode e outros clientes
compatíveis com MCP), sem conta convencional.

Esta skill ensina o agente a executar o fluxo completo. Ela funciona em par com
o [servidor MCP NAKPIX](https://github.com/nakpix/nakpix-mcp), que expõe as
ferramentas (carteiras, saldos, contatos, cupons, pagamentos, agendamentos,
incidentes).

## O que cobre

- Detecção de carteira no primeiro uso + criação Polygon/Solana.
- Backup seguro das palavras de recuperação (apenas via CLI local).
- Saldos e depósitos (USDT + gás).
- Agenda de contatos Pix.
- Cupom padrão/permanente.
- Pix imediato com cotação + confirmação configurável.
- Agendamentos e recorrências (confirmados uma vez, na criação).
- Leitura de cobranças em imagem/PDF/QR/texto (vencimentos, cobranças expiradas).
- Comprovantes e motivos reais de falha.
- Incidentes de pendência >15 minutos + e-mail ao suporte.

## Estrutura

```
SKILL.md                 # instruções do agente (comece por aqui)
references/              # documentação detalhada por tópico
examples/                # configuração MCP por cliente
```

## Instalação

1. Instale o servidor MCP (veja o README dele).
2. Registre o servidor MCP no seu cliente (veja `examples/`).
3. Instale esta skill no diretório de skills do seu agente.

## Licença

MIT — veja [LICENSE](LICENSE).
