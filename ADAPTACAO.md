# Relatório de adaptação — qualidade da água

## Problema e dados

Classificação binária de um **alerta fictício de contaminação** a partir de oito medições numéricas de água. Um operador de monitoramento usaria a saída para priorizar revisão humana; a saída não certifica potabilidade. A base é sintética, gerada localmente por `preparar_dados.py` com semente 42, 1.200 amostras (960 treino, 240 holdout, sem sobreposição). Fonte: gerador original deste repositório. Licença dos CSVs: [CC0 1.0](DATA_LICENSE.md). Não há dados pessoais ou sensíveis. O CSV de origem incerta que estava na pasta inicial foi removido.

## Contrato de qualidade

Classe positiva: `alerta_contaminacao=1`. Métrica principal: recall >= **0,85**, pois o falso negativo deixa passar um alerta simulado. Precisão >= **0,80**, para limitar falsos alertas. O candidato não pode perder mais que 0,02 de recall contra o campeão no mesmo holdout. O campeão inicial obteve recall **0,959** e precisão **0,895** em 240 amostras. Esses limiares são didáticos, não padrões sanitários.

## Alterações

| Arquivos | Adaptação |
|---|---|
| `data/raw/base_*.csv`, `preparar_dados.py` | Gerador reproduzível, alvo binário, divisão estratificada e sem sobreposição. `data/raw/lotes/` inicia vazia. |
| `config.py`, `data.py` | Oito atributos, caminhos e limiares, limites físicos, validação, preparo DVC e drift. |
| `features.py`, `train.py`, `params.yaml` | Regressão logística padronizada; parâmetros no YAML; treino do candidato e campeão. |
| `evaluate.py`, `gate.py`, `monitor.py` | Métricas da classe 1, decisão de portão e teste KS de drift. |
| `api.py`, `demo/smoke_test.py` | Schema de oito medições, rejeição de inválidos e rotas `/health` e `/predict`. |
| `tests/` | Testes novos específicos por camada, com marcadores do CI. |
| `demo/cenarios.py` | Cinco cenários para falhas e drift de água. |
| `models/champion/` | Campeão inicial regenerado com a nova base. |
| `README.md`, `DATA_LICENSE.md` | Domínio, execução, pipelines e licença. |

## Evidências do GitHub Actions

| Evidência | URL da execução |
|---|---|
| E1 CI verde na `main` | Pendente: exige repositório público da equipe |
| E2 CI vermelho controlado em PR | Pendente: exige branch e PR da equipe |
| E3 CT com drift e portão aprovado | Pendente: exige PR com lote e execução remota |
| E4 CT com portão reprovado | Pendente: exige branch/PR e execução remota |
| E5 CD verde em tag e pacote GHCR | Pendente: exige tag, Actions e publicação remota |

## Limites, riscos e contribuição

O portão usa apenas um holdout sintético e pode falhar com dados reais, mudança de distribuição, rótulos errados ou vazamento. Em produção, monitoraríamos prevalência, qualidade das medições, drift, recall/precisão com rótulos posteriores, latência e erros da API. O modelo exige validação de domínio antes de qualquer uso real.

**Contribuições:** preencher nomes e commits de cada integrante a partir do histórico do repositório da equipe. **IA:** Codex foi usado para revisar e adaptar código, testes, documentação e validação local; decisões de publicação e revisão do modelo cabem à equipe.
