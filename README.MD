# README - Problema da Dieta de Stigler

## Sobre o Projeto
Implementação do famoso "Problema da Dieta de Stigler" (1939) usando Programação Linear com Google OR-Tools. Este foi o primeiro problema de otimização linear resolvido computacionalmente.

George Stigler, economista vencedor do Nobel, formulou em 1939 o problema de encontrar a dieta de custo mínimo que atendesse a requisitos nutricionais básicos. Ele encontrou uma solução manual de US$ 39,93/ano. Anos depois, o método Simplex confirmou que sua solução estava muito próxima da ótima real.

## Perguntas Frequentes

### 1. "Por que o custo da nossa solução (R$ 261,51/ano) é diferente do Stigler original (US$ 39,93/ano)?"
Há várias razões:
- **Problema de Escala**: Os valores nutricionais nos CSVs originais parecem estar em uma escala diferente. Ex: "44.7 calorias para 10lb de farinha" é o valor do pacote inteiro, não por grama.
- **Conversão de Moeda**: Estamos tratando os preços (em cents de 1939) como Reais.
- **Inflação**: US$ 39,93 em 1939 equivalem a aproximadamente US$ 900 hoje.
- **Metodologia**: Stigler usou métodos manuais, nós usamos otimização computacional com dados ligeiramente diferentes.

### 2. "Por que o algoritmo escolhe apenas 4 alimentos? Isso é saudável?"
Não, não é saudável! Isso acontece porque:
- **Otimização Pura**: O algoritmo encontra a solução MATEMATICAMENTE ótima, não nutricionalmente balanceada
- **Eficiência de Custo**: Navy Beans, Corn Meal, Cabbage e Spinach oferecem a melhor relação nutriente/custo
- **Falta de Restrições de Variedade**: Não há restrições que forcem diversidade alimentar

### 3. "Qual algoritmo de otimização está sendo usado e por quê?"
Usamos o **Método Simplex** através do solver **GLOP** do Google OR-Tools porque:
- GLOP é especializado em problemas de Programação Linear contínua
- Simplex é eficiente para problemas com até milhares de variáveis e restrições
- Convergência Garantida para problemas convexos de PL
- Open Source, diferente de solvers comerciais como CPLEX ou Gurobi

### 4. "Por que usar variáveis contínuas (NumVar) em vez de inteiras (IntVar)?"
- **Flexibilidade**: Permite comprar frações de gramas (mais realista para alimentos)
- **Eficiência Computacional**: Problemas contínuos convergem mais rápido
- **Precisão**: Evita erros de arredondamento em quantidades pequenas

### 5. "O modelo considera sazonalidade e variação de preços?"
Não. O modelo atual usa apenas preços fixos de 1939, sem considerar sazonalidade ou variação de disponibilidade.

### 6. "Como lidar com restrições adicionais como alergias ou preferências?"
O modelo pode ser facilmente extendido:
```python
# Excluir amendoim para alérgicos
solver.Add(variaveis['Peanut Butter'] == 0)

# Limitar quantidade máxima de um alimento
solver.Add(variaveis['Liver (Beef)'] <= 50)  # Máximo 50g

# Forçar variedade mínima (aproximação)
for nome, var in variaveis.items():
    solver.Add(var <= 100)  # Limite individual
```

### 7. "A solução é viável para implementação real?"
Como está, **NÃO**, mas serve como base para:
- Protótipo educacional para aprender otimização linear
- Base para sistemas reais com adaptações:
  - Dados nutricionais atualizados
  - Preços atuais e sazonais
  - Restrições de palatabilidade e variedade
  - Considerações culturais e religiosas

### 8. "Como escalar para uma família em vez de indivíduo?"
Multiplicando os requisitos nutricionais pelo tamanho da família:
```python
tamanho_familia = 4
for nutriente in nutrientes:
    minimo_original = nutriente['minimo']
    nutriente['minimo'] = minimo_original * tamanho_familia
```

### 9. "E se não houver solução viável?"
O solver retornará `status = INFEASIBLE`. Soluções:
- Relaxar restrições mínimas
- Adicionar mais alimentos na base de dados
- Permitir déficits controlados

### 10. "Quais as principais limitações deste modelo?"
- **Dados Históricos**: Preços e valores nutricionais de 1939
- **Simplicidade Nutricional**: Considera apenas 9 nutrientes
- **Ausência de Preferências**: Não considera gosto, hábitos ou textura
- **Preços Fixos**: Não captura flutuações de mercado
- **Otimização Extrema**: Pode sugerir dietas monótonas

## Como Executar

```bash
# Instalar dependências
pip install -r requirements.txt

# Executar o script principal
python dieta_otimizada.py

# Ou abrir o Jupyter Notebook
jupyter notebook stigler.ipynb
```

## Estrutura dos Arquivos

```
/
├── dieta_otimizada.py      # Script principal (linha de comando)
├── stigler.ipynb            # Jupyter Notebook com código e explicações
├── save_instancia.ipynb     # Notebook para geração dos CSVs
├── nutrientes.csv           # Requisitos nutricionais mínimos
├── data.csv                 # Dados dos alimentos (preços e nutrientes)
├── output.txt               # Exemplo de saída do programa
├── requirements.txt         # Dependências do projeto
└── README.MD                # Este arquivo
```

## Dados Técnicos

### Nutrientes Considerados
| Nutriente | Mínimo Diário |
|-----------|--------------|
| Calorias | 3.0 kcal |
| Proteína | 70.0 g |
| Cálcio | 0.8 g |
| Ferro | 12.0 mg |
| Vitamina A | 5.0 KIU |
| Vitamina B1 | 1.8 mg |
| Vitamina B2 | 2.7 mg |
| Niacina | 18.0 mg |
| Vitamina C | 75.0 mg |

> **Nota**: Os valores nutricionais estão na escala dos dados originais de Stigler (1939), que representam o conteúdo total do pacote listado. Para valores nutricionais modernos, multiplique os requisitos por aproximadamente 100.

### Alimentos na Base
77 alimentos diferentes com preços de 1939 e perfil nutricional completo para 9 nutrientes.

## Licença
Este projeto é educacional e usa dados históricos de domínio público.
