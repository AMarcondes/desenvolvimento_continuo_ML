# Desenvolvimento Contínuo e Integração Contínua para Modelos de Aprendizagem de Máquina

# Airflow (https://airflow.apache.org/)
* Ferramenta para DATAops

* Instalacao https://airflow.apache.org/docs/stable/installation.html

```
conda install airflow
```

* Configurando base do Airflow

```
export AIRFLOW_HOME=~/airflowDB
airflow initdb
```

* Criando um script para AirFlow (DAG - Directed Acyclic Graph)


Definindo um script airflow 

Instanciando objetos:

```
#Objeto que instacia um DAG
from airflow import DAG
#Operadores, sao as tarefas do DAG, pode ser bash
from airflow.operators.bash_operator import BashOperator
# ou python
from airflow.operators.python_operator import PythonOperator
# Função utilitária de data
from airflow.utils.dates import days_ago
```

Configuracoes de inicializacao de um DAG

```
#Argumentos gerais do DAG
default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': days_ago(2),
    'email': ['airflow@example.com'],
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1
}
```

Construindo um DAG
```
dag = DAG(
    'tutorial2',
    default_args=default_args,
    description='A simple tutorial DAG22' #,
    # define intervalos regulares para executar o DAG
    #schedule_interval=timedelta(days=1),
)
```

Opcoes para intervalo de tempo da execução do DAG
```
    #schedule_interval=timedelta(days=1)
```

Tarefas do DAG
#t1, t2 e t3 sao tarefas desse workflow
t1 = BashOperator(
    task_id='print_date',
    bash_command='date',
    dag=dag,
)

t2 = BashOperator(
    task_id='sleep',
    depends_on_past=False,
    bash_command='sleep 5',
    retries=3,
    dag=dag,
)

t3 = BashOperator(
    task_id='templated',
    depends_on_past=False,
    bash_command=templated_command,
    params={'my_param': 'Parameter I passed in'},
    dag=dag,
)

Sequencia de execução das tarefas
Uma após a outra
```
t1 >> t2 >> t3 
```

T2 e t3 em paralelo após t1
```
t1 >> [t2, t3] 
```

* Preparando dados para sprint Sicoob

* Criando operadores python

* Sequencia de transformação de dados

* Depois de desenvolvido o DAG deve ser copiado para a pasta de DAGs do airflow: AIRFLOW_HOME

* Utilitários do AirFlow para manipular o DAG:
```
#print list of DAGs
airflow list_dags

# mostra tarefas de um DAG especifico
airflow list_tasks tutorialteste

# mostra hierarquia de tarefas de um DAG
airflow list_tasks tutorialteste --tree
```

* Executando tarefas de um DAG
```
airflow test tutorialteste pwd 2015-01-01
airflow test tutorialteste print_date 2015-01-01
airflow test tutorialteste print_host 2015-01-01

```
* Backfill: executa o DAG completo considerando as dependencias.Permite acompanhar o progresso da execucao na interface gráfica

```
airflow backfill tutorialteste -s 2015-06-01 -e 2015-06-07
```

Ativando interface gráfica do Airflow
```

airflow webserver
```

Exemplo de um DAG para sicob

Visao geral do DAG

* Task prep_cliente 
* Entrada : /tmp/CLIENTES_PF_NUM_CC.csv
* Saída: "/tmp/CLIENTES_CLEANED.csv"

* Task prep_mov_conta
* Entrada: /tmp/LANCAMENTOS_2019_DESC_CREDITOS.zip
* Saída: /tmp/dadosJurosAn.csv


* Task merge_mov_conta_dados_cliente
* Entrada /tmp/dadosJurosAn.csv e /tmp/CLIENTES_CLEANED.csv
* Saída: /tmp/cliente_totalCQ.csv

* Task  prep_perfil
* Entrada /tmp/Inputs_com_scores_GERAL.csv e /tmp/cliente_totalCQ.csv
* Saída: /tmp/cliente_perfil_movimentacao.csv

* Exercício: Montar um DAG para preparação de dados

