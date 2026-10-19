# Pre-commit de seguranca (secrets)

Baseline secrets-first da Azion. Objetivo unico: segredo nunca sai da estacao.
Lint, SAST, SCA e IaC nao entram aqui, ja rodam no CI. O pre-commit e o atalho de
feedback rapido; o gate obrigatorio continua sendo o gitleaks do CI.

## O que roda (bloqueante, no evento de commit, sobre o diff staged)

1. gitleaks v8.30.1 (mesma versao do CI): chave privada por conteudo, padroes de
   provider e a regra extra `azion-assigned-secret-literal` (variavel com nome de
   segredo recebendo literal opaco).
2. Guarda de arquivo sensivel por nome: `.env` real, `id_rsa`, `.p12/.pfx/.jks/`
   `.keystore/.kdbx`, `credentials.json`. Ignora `.example/.sample/.template` e
   paths de teste/fixture.
3. Limite de arquivo grande (>1MB), que de quebra barra dump/keystore por engano.

Escaneia so o diff staged, entao bloqueia apenas segredo novo introduzido no
commit. Nao varre historico.

## Instalacao na estacao (uma vez por repo)

Pre-requisito unico: `pre-commit`. Nao precisa instalar gitleaks a mao nem ter Go;
o pre-commit baixa e compila o gitleaks pinado sozinho no primeiro run.

```bash
pipx install pre-commit        # ou: pip install --user pre-commit
pre-commit install             # dentro do repo
pre-commit run --all-files     # primeiro passe, opcional
```

## Excecoes (quando for legitimo)

Da mais estreita para a mais ampla, sempre auditavel:

1. Linha isolada: sufixo `# gitleaks:allow` na propria linha.
2. Repo inteiro: `regexes`/`paths` no `.gitleaks.toml` do proprio repo (o allowlist
   e repo-especifico; a base canonica padroniza so o tronco comum).
3. Break-glass pontual: `git commit --no-verify`. Nao e controle: o gitleaks do CI,
   obrigatorio, pega o que a estacao pulou. Use so em emergencia e rotacione o segredo.

## Nota tecnica para quem escrever regra nova

gitleaks usa RE2 (Go), que nao suporta lookahead. Exigir entropia por regex faz o
config abortar no load. A entropia minima se declara pelo campo `entropy` da regra.

## Versao

gitleaks `v8.30.1`, pre-commit-hooks `v5.0.0`. Ao subir versao, subir junto no CI e
aqui para estacao e gate nao divergirem. Fonte canonica deste baseline: `aziontech/.github`.
