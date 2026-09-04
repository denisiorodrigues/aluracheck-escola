# Guia de Contribuição

## Fluxo de trabalho

1. Toda tarefa começa como uma **Issue** (bug, feature ou chore), com label e, se possível, associada a um **Milestone**.
2. As Issues em andamento vivem no **Project (board)** do repositório, nas colunas: `Backlog → Ready → In progress → In review → Done`.
3. Para trabalhar em uma issue, crie uma branch a partir da `main`:

   ```bash
   git checkout main
   git pull origin main
   git checkout -b feature/nome-curto-da-tarefa
   ```

## Branches

- `main`: sempre estável e deployável. Ninguém commita direto nela.
- `feature/*`: qualquer trabalho novo (feature, fix, chore) parte daqui e volta via Pull Request.

Convenção de nomes:

| Prefixo | Uso |
|---|---|
| `feature/*` | nova funcionalidade |
| `fix/*` | correção de bug |
| `chore/*` | manutenção, dependências, config |
| `docs/*` | documentação |

## Commits (Conventional Commits)

Formato:

```
<tipo>(<escopo opcional>): <descrição curta>
```

Tipos: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`.

Exemplos:

```
feat(auth): adiciona login via Google
fix(checkout): corrige cálculo de frete com cupom
chore(deps): atualiza dependências
```

## Pull Requests

- Abra o PR de `feature/*` para `main` usando o template automático.
- Referencie a issue relacionada com `Closes #123` na descrição — isso fecha a issue automaticamente ao dar merge.
- É necessário pelo menos 1 aprovação e os checks de CI passando antes do merge.
- Prefira **squash merge** para manter o histórico da `main` limpo (um commit por PR, seguindo Conventional Commits).

## Milestones

- Cada Issue relevante deve ser associada a um Milestone (versão ou sprint).
- Milestones são fechados quando todas as issues associadas são concluídas.
