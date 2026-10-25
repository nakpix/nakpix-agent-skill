# Contacts (persistent PIX agenda)

## Tools

- `nakpix_add_contact(nome, chave)`
- `nakpix_list_contacts`
- `nakpix_get_contact(nome)`
- `nakpix_find_contact(nome)`
- `nakpix_update_contact(nome, chave)`
- `nakpix_delete_contact(nome)`

## Behavior

- Names are matched case/whitespace-insensitively. Partial search with
  `nakpix_find_contact`.
- Keys are validated (CPF, CNPJ, telefone +55, email, EVP). Invalid keys are
  rejected — never saved.
- The agenda persists across restarts (per install/identity).

## Flow

```
Usuário: "Faz um Pix de R$ 100 para meu pai."
Agente:  nakpix_find_contact("pai")
         → found? use the key.
         → not found? "Não encontrei uma chave Pix cadastrada para Pai.
                        Qual é a chave dele?"
         → user provides key → validate → offer to save it.
```

A one-off PIX without saving the contact is fine.
