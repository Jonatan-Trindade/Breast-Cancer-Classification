<p align="center">
  <img src="banner.jpg" alt="Breast Cancer Classification - Machine Learning" width="100%">
</p>


# 🩺 Breast Cancer Classification — Machine Learning

Projeto de Machine Learning para classificação de tumores mamários como benignos ou malignos, usando o Breast Cancer Wisconsin Diagnostic Dataset (WDBC).

O foco é detectar o máximo possível de casos malignos (recall), comparando três algoritmos, interpretando o que sustenta as previsões e avaliando com cuidado o que os resultados realmente permitem concluir.

⚠️ Aviso: projeto com finalidade exclusivamente educacional. O modelo não deve ser utilizado para diagnóstico médico real e não passou por validação clínica.

🎯 Objetivo

Classificar tumores como 🟢 Benigno (0) ou 🔴 Maligno (1) e responder:

Quais grupos de características (tamanho, concavidade) sustentam a previsão?
Os modelos se distinguem de forma estatisticamente relevante?
Ajustar o limiar de decisão reduz os malignos não detectados? A que custo?
📊 Dataset

Breast Cancer Wisconsin Diagnostic Dataset (WDBC): 569 pacientes (37% malignos), sem valores ausentes, com 30 características morfológicas dos núcleos celulares (média, erro padrão e pior valor de raio, textura, área, concavidade etc.). Arquivos em data/.

🧪 Metodologia
Análise exploratória e divisão treino/teste estratificada (70/30; teste com 171 pacientes, 64 malignos).
Modelos: Regressão Logística (com StandardScaler), Random Forest e XGBoost (padrão e com ajuste de hiperparâmetros).
Interpretação: odds ratio com intervalo de confiança (bootstrap), VIF, SHAP e permutation importance (individual e em grupo) nos três modelos.
Validação cruzada estratificada de 5 folds, inclusive por cenários e blocos de variáveis.
Teste exato de McNemar entre modelos.
Ajuste do limiar de decisão escolhido apenas com o treino (validação cruzada) e confirmado uma única vez no teste.
🔍 Principais resultados
Desempenho em validação cruzada (5 folds, 569 pacientes)
Modelo	Acurácia	Precisão	Recall	F1	ROC-AUC
Regressão Logística	0,9737	0,9863	0,9436	0,9633	0,9953
Random Forest	0,9526	0,9461	0,9295	0,9355	0,9895
XGBoost	0,9613	0,9588	0,9389	0,9473	0,9936
XGBoost otimizado	0,9666	0,9583	0,9528	0,9547	0,9936

As diferenças de AUC são menores que os desvios padrão; os modelos empatam na capacidade de separar as classes. O recall varia entre 0,93 e 0,95 nos quatro.

Tamanho e concavidade se complementam

Combinar os blocos de tamanho e concavidade melhorou o AUC em cerca de 0,01 a 0,04 em relação a cada bloco isolado, nos três modelos. Como as diferenças são próximas dos desvios, isso é um indício, não uma prova. Pela forte colinearidade (VIF de até 3995), a leitura segura é por bloco, não por variável isolada.

Os modelos não se distinguem estatisticamente

O McNemar (limiar 0,5, 171 pacientes) não encontrou diferença entre XGBoost e Regressão Logística (p = 0,6875), nem entre Random Forest e Regressão Logística (p = 1,0). Isso indica ausência de evidência de diferença, não equivalência: o teste tem baixo poder.

Ajuste do limiar de decisão

O limiar foi escolhido só com o treino: baixar de 0,5 para 0,3 reduziu os malignos perdidos de 7 para 4 (de 148), ao custo de 10 falsos alarmes (de 250). Resultado no teste:

Modelo (teste, 64 malignos)	Limiar	FN	FP	Recall	Precisão
Regressão Logística	0,5	4	1	93,75%	98,36%
Regressão Logística	0,3	2	1	96,88%	98,41%
Random Forest	0,5	4	0	93,75%	100%
XGBoost	0,5	3	0	95,31%	100%
XGBoost otimizado	0,5	3	0	95,31%	100%

O ganho do limiar é de 2 pacientes. O intervalo de confiança de 95% do recall da LR a 0,3 vai de cerca de 89% a 99%, e o do XGBoost, de 87% a 98%: eles se sobrepõem, então o teste não permite afirmar que um modelo é melhor que o outro.

🏆 Modelo final

Regressão Logística (StandardScaler) com limiar 0,3, pela interpretabilidade do sentido dos efeitos, baixo custo computacional, simplicidade de manutenção e recall competitivo.

⚠️ Limitações
O teste tem 64 malignos: trocar 1 ou 2 pacientes muda o recall em 1,6 a 3,1 pontos percentuais.
O limiar 0,3 se apoia em poucos erros na validação cruzada (4 a 7 malignos perdidos).
Os coeficientes individuais são instáveis (colinearidade e intervalos sobrepostos).
O limiar do XGBoost não foi ajustado, então a comparação com a LR a 0,3 não é totalmente simétrica.
Os hiperparâmetros do XGBoost foram escolhidos com os dados inteiros, o que deixa o resultado otimista.
📈 Visualizações
Recall da classe maligna no teste

Recall no teste

Eixo truncado em 85% para destacar as diferenças. Com 64 malignos no teste, cada paciente vale 1,56 ponto percentual.

SHAP dos três modelos

Cada ponto é um paciente: à direita empurra a previsão para maligno, à esquerda para benigno. Os valores do Random Forest estão em probabilidade e os da Regressão Logística e do XGBoost em log-odds, então só a ordem das variáveis é comparável entre eles.

Regressão Logística	Random Forest	XGBoost
SHAP LR	SHAP RF	SHAP XGB
🛠️ Tecnologias

Python · Pandas · NumPy · Scikit-learn · XGBoost · SHAP · Statsmodels · Seaborn · Matplotlib · Jupyter Notebook

📂 Estrutura
Breast-Cancer-Classification/
├── README.md
├── LICENSE
├── data/            # wdbc.data, wdbc.names
├── images/
├── animation/
└── notebook/        # breast_cancer.ipynb
🚀 Como executar
bash
git clone https://github.com/Jonatan-Trindade/Breast-Cancer-Classification.git
cd Breast-Cancer-Classification
python -m venv .venv
.venv\Scripts\activate        # Windows
pip install pandas numpy scikit-learn xgboost shap seaborn matplotlib statsmodels jupyter

Abra notebook/breast_cancer.ipynb e execute as células.

👨‍💻 Autor

Jonatan Trindade — LinkedIn · GitHub
