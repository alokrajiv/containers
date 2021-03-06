---

copyright:
  years: 2014, 2018
lastupdated: "2018-10-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}




# Implementando apps em clusters
{: #app}

É possível usar técnicas do Kubernetes no {{site.data.keyword.containerlong}} para implementar apps em contêineres e assegurar que os apps estejam funcionando sempre. Por exemplo, é possível executar atualizações e recuperações contínuas sem tempo de inatividade para seus usuários.
{: shortdesc}

Aprenda as etapas gerais para implementar apps clicando em uma área da imagem a seguir. Deseja aprender o básico primeiro? Experimente o  [ tutorial de implementação de apps ](cs_tutorials_apps.html#cs_apps_tutorial).

<img usemap="#d62e18" border="0" class="image" id="basic_deployment_process" src="images/basic_deployment_process.png" width="780" style="width:780px;" alt="Processo de implementação básica"/>
<map name="d62e18" id="d62e18">
<area href="cs_cli_install.html" target="_blank" alt="Instalar as CLIs." title="Instalar as CLIs." shape="rect" coords="30, 69, 179, 209" />
<area href="https://kubernetes.io/docs/concepts/configuration/overview/" target="_blank" alt="Crie um arquivo de configuração para o seu app. Revise as melhores práticas do Kubernetes." title="Crie um arquivo de configuração para o seu app. Revise as melhores práticas do Kubernetes." shape="rect" coords="254, 64, 486, 231" />
<area href="#app_cli" target="_blank" alt="Opção 1: execute os arquivos de configuração da CLI do Kubernetes." title="Opção 1: execute os arquivos de configuração da CLI do Kubernetes." shape="rect" coords="544, 67, 730, 124" />
<area href="#cli_dashboard" target="_blank" alt="Opção 2: inicie o painel do Kubernetes localmente e execute os arquivos de configuração." title="Opção 2: inicie o painel do Kubernetes localmente e execute os arquivos de configuração." shape="rect" coords="544, 141, 728, 204" />
</map>

<br />


## Planejamento para executar apps em clusters
{: #plan_apps}

Assegure-se de que seu app esteja pronto para implementação no {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

### Que tipo de objetos do Kubernetes posso fazer para meu app?
{: #object}

Ao preparar seu arquivo YAML do app, você tem muitas opções para aumentar a disponibilidade, o desempenho e a segurança do app. Por exemplo, em vez de um único pod, é possível usar um objeto de controlador do Kubernetes para gerenciar sua carga de trabalho, como um conjunto de réplicas, uma tarefa ou um conjunto de daemons. Para obter mais informações sobre pods e controladores, visualize a [documentação do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/). Uma implementação que gerencia um conjunto de réplicas de pods é um caso de uso comum para um app.

Por exemplo, um objeto `kind: Deployment` é uma boa opção para implementar um pod de app porque, com ele, é possível especificar um conjunto de réplicas para obter mais disponibilidade para seus pods.

A tabela a seguir descreve por que é possível criar diferentes tipos de objetos de carga de trabalho do Kubernetes.

| Objeto | Descrição | 
| --- | --- |
| [`Pod` ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/pods/pod/) | Um pod é a menor unidade implementável para suas cargas de trabalho e pode conter um ou mais contêineres. Semelhante a contêineres, os pods são projetados para serem descartáveis e são frequentemente usados para teste de unidade de recursos de app. Para evitar tempo de inatividade para seu app, considere a implementação de pods com um controlador do Kubernetes, como uma implementação. Uma implementação ajuda a gerenciar múltiplos pods, réplicas, ajuste de escala de pod, lançamentos e mais. |
| [`ReplicaSet` ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) | Um conjunto de réplicas assegura que múltiplas réplicas de seu pod estejam em execução e reagenda um pod se ele fica inativo. Você pode criar um conjunto de réplicas para testar como o planejamento de pod funciona, mas para gerenciar atualizações de aplicativo, lançamentos e ajuste de escala, crie uma implementação em seu lugar. |
| [`Deployment` ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) | Uma implementação é um controlador que gerencia um pod ou um [conjunto de réplicas ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) de modelos de pod. É possível criar pods ou conjuntos de réplicas sem uma implementação para testar recursos de app. Para uma configuração de nível de produção, use implementações para gerenciar atualizações de app, lançamentos e ajuste de escala. |
| [`StatefulSet` ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) | Semelhante a implementações, um conjunto stateful é um controlador que gerencia um conjunto de réplicas de pods. Diferentemente de implementações, um conjunto stateful assegura que seu pod tenha uma identidade de rede exclusiva que mantém seu estado no reagendamento. Quando você desejar executar as cargas de trabalho na nuvem, tente projetar seu app para ser stateless para que suas instâncias de serviço sejam independentes umas das outras e possam falhar sem uma interrupção de serviço. No entanto, alguns apps, como bancos de dados, devem ser stateless. Para esses casos, considere criar um conjunto stateful e usar o armazenamento de [arquivo](cs_storage_file.html#file_statefulset), [bloco](cs_storage_block.html#block_statefulset) ou [objeto](cs_storage_cos.html#cos_statefulset) como o armazenamento persistente para seu conjunto stateful. |
| [`DaemonSet` ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) | Use um daemonset quando tiver que executar o mesmo pod em cada nó do trabalhador em seu cluster. Os pods que são gerenciados por um daemonset são planejados automaticamente quando um nó do trabalhador é incluído em um cluster. Os casos de uso típicos incluem coletores de log, como `logstash` ou `prometheus`, que coletam logs de cada nó do trabalhador para fornecer insight sobre o funcionamento de um cluster ou um app. |
| [`Job` ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/) | Uma tarefa assegura que um ou mais pods sejam executados com êxito até a conclusão. Você pode usar uma tarefa para filas ou tarefas em lote para suportar o processamento paralelo de itens de trabalho separados, porém relacionados, como um determinado número de quadros a serem renderizados, e-mails a serem enviados e arquivos a serem convertidos. Para planejar uma tarefa para ser executada em determinados momentos, use uma [Tarefa Cron ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/).|
{: caption="Tipos de objetos de carga de trabalho do Kubernetes que podem ser criados." caption-side="top"}

### Como posso incluir recursos em minha configuração do aplicativo do Kubernetes?
Veja [Especificando seus requisitos de app em seu arquivo YAML](#app_yaml) para obter descrições do que você pode incluir em uma implementação. O exemplo inclui:
* [ Conjuntos de réplicas ](#replicaset)
* [Identificadores](#label)
* [ Afinidade ](#affinity)
* [ Políticas de Imagem ](#image)
* [Portas](#port)
* [ Solicitações de recurso e limites ](#resourcereq)
* [ Análises de prontidão e prontidão ](#probe)
* [Serviços](#service) para expor o serviço de app em uma porta
* [ Configmaps ](#configmap)  para configurar variáveis de ambiente de contêiner
* [ Secrets ](#secret)  para configurar variáveis de ambiente de contêiner
* [Volumes persistentes](#pv) que são montados para o contêiner para armazenamento

### E se eu desejar que a configuração do aplicativo do Kubernetes use variáveis? Como incluí-las no YAML?
{: #variables}

Para incluir informações de variável em suas implementações em vez de codificar permanentemente os dados no arquivo YAML, é possível usar um [`ConfigMap` do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) ou um objeto [`Secret` ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/secret/).

Para consumir um configmap ou segredo, é necessário montá-lo no pod. O configmap ou segredo é combinado com o pod exatamente antes de o pod ser executado. É possível reutilizar uma especificação de implementação e imagem em muitos apps, mas, em seguida, descarregar para a área de troca os configmaps e segredos customizados. Os segredos em particular podem ocupar muito armazenamento no nó local, portanto, planeje adequadamente.

Ambos os recursos definem os pares chave-valor, mas são usados para diferentes situações.

<dl>
<dt>Configmap</dt>
<dd>Forneça informações de configuração não confidenciais para cargas de trabalho que são especificadas em uma implementação. É possível usar configmaps de três maneiras principais.
<ul><li><strong>Sistema de arquivos</strong>: é possível montar um arquivo inteiro ou um conjunto de variáveis em um pod. Um arquivo é criado para cada entrada com base nos conteúdos de nome da chave do arquivo que são configurados para o valor.</li>
<li><strong>Variável de ambiente</strong>: configure dinamicamente a variável de ambiente para uma especificação de contêiner.</li>
<li><strong>Argumento de linha de comandos</strong>: configure o argumento de linha de comandos que é usado em uma especificação de contêiner.</li></ul></dd>

<dt>Segredo</dt>
<dd>Forneça informações confidenciais para suas cargas de trabalho, como a seguir. Observe que outros usuários do cluster podem ter acesso ao segredo, portanto, certifique-se de que saiba que as informações secretas podem ser compartilhadas com esses usuários.
<ul><li><strong>Informações pessoalmente identificáveis (PII)</strong>: armazene informações confidenciais, como endereços de e-mail ou outros tipos de informações que são necessárias para conformidade de empresa ou regulamentação governamental em segredos.</li>
<li><strong>Credenciais</strong>: coloque credenciais, como senhas, chaves e tokens, em um segredo para reduzir o risco de exposição acidental. Por exemplo, quando você [liga um serviço](cs_integrations.html#adding_cluster) a seu cluster, as credenciais são armazenadas em um segredo.</li></ul></dd>
</dl>

Deseja tornar seus segredos ainda mais seguros? Peça ao administrador do cluster para [ativar o {{site.data.keyword.keymanagementservicefull}}](cs_encrypt.html#keyprotect) em seu cluster para criptografar segredos novos e existentes.
{: tip}

### Como posso incluir serviços da IBM em meu app, como o Watson?
Consulte  [ Incluindo serviços em apps ](cs_integrations.html#adding_app).

### Como posso ter certeza de que meu app tem os recursos certos?
Quando você [especifica seu arquivo YAML do app](#app_yaml), é possível incluir funcionalidades do Kubernetes em sua configuração do aplicativo que ajudam seu app a obter os recursos certos. Em particular, [configure limites de recurso e solicitações ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/) para cada contêiner que está definido em seu arquivo YAML.

Além disso, seu administrador de cluster pode configurar controles de recurso que podem afetar a implementação do app, conforme a seguir.
*  [Cotas de recurso ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/policy/resource-quotas/)
*  [ Prioridade de Pod ](cs_pod_priority.html#pod_priority)

### Como posso acessar meu app?
É possível acessar seu app privadamente dentro do cluster [usando um serviço `clusterIP`](cs_network_cluster.html#planning).

Se desejar expor publicamente seu app, você terá opções diferentes que dependem do tipo de cluster.
*  **Cluster grátis**: é possível expor seu app usando um [serviço NodePort](cs_nodeport.html#nodeport).
*  **Cluster padrão**: é possível expor seu app usando um [serviço NodePort, balanceador de carga ou Ingress](cs_network_planning.html#planning).
*  **Cluster que se torna privado usando o Calico**: é possível expor seu app usando um [serviço NodePort, balanceador de carga ou Ingress](cs_network_planning.html#private_both_vlans). Deve-se também usar uma política de rede preDNAT do Calico para bloquear as portas de nó público.
*  **Cluster padrão somente de VLAN privada**: é possível expor seu app usando um [serviço NodePort, balanceador de carga ou Ingress](cs_network_planning.html#private_vlan). Deve-se também abrir a porta para o endereço IP privado do serviço em seu firewall.

### Depois de implementar meu app, como posso monitorar seu funcionamento?
É possível configurar a [criação de log e monitoramento](cs_health.html#health) do {{site.data.keyword.Bluemix_notm}} para seu cluster. Também é possível escolher integrar a um [serviço de criação de log ou de monitoramento](cs_integrations.html#health_services) de terceiros.

### Como posso manter meu app atualizado?
Se desejar incluir e remover apps dinamicamente em resposta ao uso de carga de trabalho, veja [Escalando apps](cs_app.html#app_scaling).

Se desejar gerenciar atualizações para seu app, veja [Gerenciando implementações contínuas](cs_app.html#app_rolling).

### Como posso controlar quem tem acesso às minhas implementações de app?
Os administradores de conta e cluster podem controlar o acesso em vários níveis diferentes: cluster, namespace do Kubernetes, pod e contêiner.

Com o IAM, é possível designar permissões para usuários individuais, grupos ou contas de serviço no nível de instância de cluster.  É possível definir o escopo de acesso do cluster ainda mais, restringindo os usuários a namespaces específicos dentro do cluster. Para obter mais informações, veja [Designando acesso ao cluster](cs_users.html#users).

Para controlar o acesso no nível de pod, é possível [configurar políticas de segurança de pod com o RBAC do Kubernetes](cs_psp.html#psp).

Dentro do YAML de implementação do app, é possível configurar o contexto de segurança para um pod ou contêiner. Para obter mais informações, revise a [documentação do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/).

<br />


## Planejando implementações altamente disponíveis
{: #highly_available_apps}

Quanto mais amplamente você distribui a configuração entre múltiplos nós do trabalhador e clusters,
menos provável que os usuários tenham que experimentar tempo de inatividade com seu app.
{: shortdesc}

Revise as potenciais configurações de app a seguir que são ordenadas com graus crescentes de disponibilidade.

![Estágios de alta disponibilidade para um app](images/cs_app_ha_roadmap-mz.png)

1.  Uma implementação com n+2 pods que são gerenciados por um conjunto de réplicas em um único nó em um cluster de zona única.
2.  Uma implementação com n+2 pods que são gerenciados por um conjunto de réplicas e difundidos em múltiplos nós (antiafinidade) em um cluster de zona única.
3.  Uma implementação com n+2 pods que são gerenciados por um conjunto de réplicas e difundidos entre múltiplos nós (antiafinidade) em um cluster de múltiplas zonas entre zonas.

Também é possível [conectar múltiplos clusters em regiões diferentes com um balanceador de carga global](cs_clusters_planning.html#multiple_clusters) para aumentar a alta disponibilidade.

### Aumentando a disponibilidade de seu app
{: #increase_availability}

<dl>
  <dt>Usar implementações e conjuntos de réplicas para implementar seu app e suas dependências</dt>
    <dd><p>Uma implementação é um recurso do Kubernetes que pode ser usado para declarar todos os componentes de seu app e suas dependências. Com as implementações, não é necessário anotar todas as etapas e, em vez disso, é possível se concentrar no app.</p>
    <p>Ao implementar mais de um pod, um conjunto de réplicas é criado automaticamente para as suas
implementações e monitora os pods e assegura que o número desejado de pods esteja ativo e em execução
sempre. Quando um pod fica inativo, o conjunto de réplicas substitui o pod não responsivo por um novo.</p>
    <p>É possível usar uma implementação para definir estratégias de atualização para seu app incluindo o número de
módulos que você deseja incluir durante uma atualização contínua e o número de pods que podem estar indisponíveis
por vez. Ao executar uma atualização contínua, a implementação verifica se a revisão está ou não
funcionando e para o lançamento quando falhas são detectadas.</p>
    <p>Com as implementações, é possível implementar simultaneamente múltiplas revisões com diferentes sinalizações. Por exemplo, é possível testar uma implementação primeiro antes de decidir enviá-la por push para a produção.</p>
    <p>As implementações permitem manter o controle de qualquer revisão implementada. Será possível usar esse histórico para recuperar uma versão anterior se você descobrir que as suas atualizações não estão funcionando conforme o esperado.</p></dd>
  <dt>Incluir réplicas suficientes para a carga de trabalho de seu app, mais duas</dt>
    <dd>Para tornar seu app ainda mais altamente disponível e mais resiliente à falha, considere a inclusão
de réplicas extras, além do mínimo, para manipular a carga de trabalho esperada. As réplicas extras podem manipular a
carga de trabalho no caso de um pod travar e o conjunto de réplicas ainda não tiver recuperado o pod travado. Para
proteção contra duas falhas simultâneas, inclua duas réplicas extras. Essa configuração é um padrão N + 2, em que N é o número de réplicas para manipular a carga de trabalho recebida e + 2 são duas réplicas extras. Desde que seu cluster tenha espaço suficiente, será possível ter tantos pods quantos você quiser.</dd>
  <dt>Difundir pods em múltiplos nós (antiafinidade)</dt>
    <dd><p>Quando você cria a sua implementação, cada pod pode ser implementado no mesmo nó do trabalhador. Isso é conhecido como afinidade ou colocação. Para proteger seu app contra falha do nó do trabalhador, será possível configurar sua implementação para difundir os pods em múltiplos nós do trabalhador usando a opção <em>podAntiAffinity</em> com seus clusters padrão. É possível definir dois tipos de antiafinidade do pod: preferencial ou necessário.
      <p>Para obter mais informações, veja a documentação do Kubernetes no <a href="https://kubernetes.io/docs/concepts/configuration/assign-pod-node/" rel="external" target="_blank" title="(Abre em uma nova guia ou janela)">Designando pods aos nós</a>.</p>
      <p>Para obter um exemplo de afinidade em uma implementação de app, veja [Fazendo seu arquivo YAML de implementação de app](#app_yaml).</p>
      </dd>
    </dd>
<dt>Distribuir pods em múltiplas zonas ou regiões</dt>
  <dd><p>Para proteger seu app de uma falha de zona, é possível criar múltiplos clusters em zonas separadas ou incluir zonas em um conjunto de trabalhadores em um cluster de múltiplas zonas. Os clusters de múltiplas zonas estão disponíveis somente em [determinadas áreas metropolitanas](cs_regions.html#zones), como Dallas. Se você cria múltiplos clusters em zonas separadas, deve-se [configurar um balanceador de carga global](cs_clusters_planning.html#multiple_clusters).</p>
  <p>Ao usar um conjunto de réplicas e especificar a antiafinidade do pod, o Kubernetes difunde seus pods de app entre os nós. Se os seus nós estiverem em múltiplas zonas, os pods serão difundidos pelas zonas, aumentando a disponibilidade do seu app. Se você desejar limitar seus apps para serem executados somente em uma zona, será possível configurar a afinidade de pod ou criar e rotular um conjunto de trabalhadores em uma zona. Para obter mais informações, consulte [Alta disponibilidade para clusters de múltiplas zonas](cs_clusters_planning.html#ha_clusters).</p>
  <p><strong>Em uma implementação de cluster de múltiplas zonas, meus pods de app são distribuídos uniformemente entre os nós?</strong></p>
  <p>Os pods são distribuídos uniformemente entre as zonas, mas nem sempre entre os nós. Por exemplo, se você tiver um cluster com 1 nó em cada uma das 3 zonas e implementar um conjunto de réplicas de 6 pods, cada nó obterá dois pods. No entanto, se você tiver um cluster com 2 nós em cada uma das 3 zonas e implementar um conjunto de réplicas de 6 pods, cada zona terá 2 pods planejados e poderá ou não planejar 1 pod por nó. Para obter mais controle sobre o planejamento, é possível [configurar a afinidade de pod ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/assign-pod-node).</p>
  <p><strong> Se uma zona ficar inativa, como os pods serão reprogramados para os nós restantes nas outras zonas?</strong></br>Isso depende da política de planejamento que você usou na implementação. Se você incluiu a [afinidade de pod específica do nó ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#node-affinity-beta-feature), seus pods não serão reprogramados. Se você não tiver feito isso, os pods serão criados em nós do trabalhador disponíveis em outras zonas, mas eles podem não ser balanceados. Por exemplo, os 2 pods podem ser difundidos entre os 2 nós disponíveis ou ambos podem ser planejados para 1 nó com capacidade disponível. Da mesma forma, quando a zona indisponível retornar, os pods não serão excluídos e rebalanceados automaticamente entre os nós. Se desejar que os pods sejam rebalanceados entre as zonas depois que a zona estiver de volta, considere usar o [Desplanejador do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/kubernetes-incubator/descheduler).</p>
  <p><strong>Dica</strong>: em clusters de múltiplas zonas, tente manter a capacidade do seu nó do trabalhador em 50% por zona, para que você tenha capacidade suficiente para proteger o seu cluster com relação a uma falha zonal.</p>
  <p><strong>E se eu desejar difundir meu app entre regiões?</strong></br>Para proteger seu app de uma falha de região, crie um segundo cluster em outra região, [configure um balanceador de carga global](cs_clusters_planning.html#multiple_clusters) para conectar seus clusters e use um YAML de implementação para implementar um conjunto de réplicas duplicado com [antiafinidade de pod ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) para seu app.</p>
  <p><strong>E se meus apps precisarem de armazenamento persistente?</strong></p>
  <p>Use um serviço de nuvem como o [{{site.data.keyword.cloudant_short_notm}}](/docs/services/Cloudant/getting-started.html#getting-started-with-cloudant) ou o [{{site.data.keyword.cos_full_notm}}](/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage).</p></dd>
</dl>

## Especificando os requisitos de app em seu arquivo YAML
{: #app_yaml}

No Kubernetes, você descreve seu app em um arquivo YAML que declara a configuração desejada do objeto do Kubernetes. O servidor de API do Kubernetes, então, processa o arquivo YAML e armazena a configuração e o estado desejado do objeto no armazenamento de dados etcd. O planejador do Kubernetes planeja suas cargas de trabalho nos nós do trabalhador dentro de seu cluster, levando em consideração a especificação em seu arquivo YAML, quaisquer políticas de cluster que o administrador configura e a capacidade disponível do cluster.
{: shortdesc}

Revise uma cópia do [arquivo YAML completo](https://raw.githubusercontent.com/IBM-Cloud/kube-samples/master/deploy-apps-clusters/deploy_wasliberty.yaml). Em seguida, revise as seções a seguir para entender como é possível aprimorar a implementação do app.

* [ Conjuntos de réplicas ](#replicaset)
* [Identificadores](#label)
* [ Afinidade ](#affinity)
* [ Políticas de Imagem ](#image)
* [Portas](#port)
* [ Solicitações de recurso e limites ](#resourcereq)
* [ Análises de prontidão e prontidão ](#probe)
* [Serviços](#service) para expor o serviço de app em uma porta
* [ Configmaps ](#configmap)  para configurar variáveis de ambiente de contêiner
* [ Secrets ](#secret)  para configurar variáveis de ambiente de contêiner
* [Volumes persistentes](#pv) que são montados para o contêiner para armazenamento
* [Etapas seguintes](#nextsteps)
* [ Exemplo Completo de YAML ](#yaml-example)

<dl>
<dt>Metadados de implementação básica</dt>
  <dd><p>Use a versão apropriada da API para o [tipo de objeto do Kubernetes](#object) que você implementa. A versão da API determina os recursos suportados para o objeto do Kubernetes que estão disponíveis para você. O nome fornecido nos metadados é o nome do objeto, não seu rótulo. Você usa o nome ao interagir com seu objeto, como `kubectl get deployment <name>`.</p>
  <p><pre class="codeblock"><code> apiVersion: apps / v1beta1
Tipo: implementação
metadados:
  name: wasliberty </code></pre></p></dd>

<dt id="replicaset">Conjunto de réplicas</dt>
  <dd><p>Para aumentar a disponibilidade de seu app, é possível especificar um conjunto de réplicas em sua implementação. Em um conjunto de réplicas, são definidas quantas instâncias de seu app você deseja implementar. Os conjuntos de réplicas são gerenciados e monitorados por sua implementação do Kubernetes. Se uma instância do app fica inativa, o Kubernetes acelera automaticamente uma nova instância de seu app para manter o número especificado de instâncias do app.</p>
  <p><pre class="codeblock"><code> spec:
  replicas: 3</pre></code></p></dd>

<dt id="label">Identificadores</dt>
  <dd><p>Com rótulos, é possível marcar diferentes tipos de recursos em seu cluster com o mesmo par `key: value`. Em seguida, é possível especificar o seletor para corresponder ao rótulo para que seja possível construir sobre esses outros recursos. Se você planeja expor seu app publicamente, deve-se usar um rótulo que corresponda ao seletor especificado no serviço. No exemplo, a especificação de implementação usa o modelo que corresponde ao rótulo `app: wasliberty`.</p>
  <p>É possível recuperar objetos que são rotulados em seu cluster, como para ver os componentes `staging` ou `production`. Por exemplo, liste todos os recursos com um rótulo `env: production` em todos os namespaces no cluster. Observe que você precisa de acesso a todos os namespaces para executar esse comando.<pre class="pre"><code> kubectl get all -l env=production -- all-namespaces </code></pre></p>
  <ul><li>Para obter mais informações sobre rótulos, veja a [documentação do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/).</li>
  <li>Para obter um exemplo mais detalhado, veja [Implementando apps em nós do trabalhador específicos usando rótulos](cs_app.html#node_affinity).</li></ul>
  <p><pre class="codeblock"><code> selector:
  matchLabels:
    app: wasliberty
:
  metadados:
    labels:
      app: wasliberty</pre></code></p></dd>

<dt id="affinity">Afinidade</dt>
  <dd><p>Especifique a afinidade (colocação) quando desejar obter mais controle sobre em quais nós do trabalhador os pods estão planejados. A afinidade afeta somente os pods no tempo de planejamento. Por exemplo, para difundir a implementação em nós do trabalhador em vez de permitir que os pods sejam planejados no mesmo nó, use a opção <code>podAntiAffinity</code> com seus clusters padrão. É possível definir dois tipos de antiafinidade do pod: preferencial ou necessário.</p>
  <p>Para obter mais informações, veja a documentação do Kubernetes no <a href="https://kubernetes.io/docs/concepts/configuration/assign-pod-node/" rel="external" target="_blank" title="(Abre em uma nova guia ou janela)">Designando pods aos nós</a>.</p>
  <ul><li><strong>Antiafinidade necessária</strong>: é possível implementar somente a quantia de réplicas para as quais você tem nós do trabalhador. Por exemplo, se você tiver 3 nós do trabalhador no cluster, mas definir 5 réplicas no arquivo YAML, apenas 3 réplicas serão implementadas. Cada réplica mora em um nó de trabalhador diferente. Os restantes 2 réplicas continuam pendentes. Se você incluir outro nó do trabalhador no cluster, uma das réplicas restantes será implementada no novo nó do trabalhador automaticamente. Se um nó do trabalhador falhar, o pod não reagendará porque a política de afinidade é necessária. Para obter um YAML de exemplo com necessário, veja <a href="https://github.com/IBM-Cloud/kube-samples/blob/master/deploy-apps-clusters/liberty_requiredAntiAffinity.yaml" rel="external" target="_blank" title="(Abre em uma nova guia ou janela)">App Liberty com antiafinidade de pod necessária</a>.</li>
  <li><strong>Antiafinidade preferencial</strong>: é possível implementar seus pods em nós com capacidade disponível, o que fornece mais flexibilidade para sua carga de trabalho. Quando possível, os pods são planejados em diferentes nós do trabalhador. Por exemplo, se você tiver 3 nós do trabalhador com capacidade suficiente em seu cluster, ele poderá planejar os 5 pods de réplica em todos os nós. No entanto, se você incluir mais dois nós do trabalhador em seu cluster, a regra de afinidade não forçará os 2 pods extras em execução nos nós existentes para reagendar para o nó grátis.</li>
  <li><strong>Afinidade do nó do trabalhador</strong>: é possível configurar a implementação para ser executada somente em determinados nós do trabalhador, como bare metal. Para obter mais informações, veja [Implementando apps em nós do trabalhador específicos usando rótulos](cs_app.html#node_affinity).</li></ul>
  <p>Exemplo para a antiafinidade preferencial:</p>
  <p><pre class="codeblock"><code> spec:
  affinity:
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
            podAffinityTerm:
              labelSelector:
                matchExpressions:
            - key: app
                  operator: In
                  values:
              - wasliberty
          topologyKey: kubernetes.io/hostname</pre></code></p></dd>

<dt id="image">Imagem do contêiner</dt>
  <dd>
  <p>Especifique a imagem que você deseja usar para seus contêineres, o local da imagem e a política de pull da imagem. Se você não especificar uma tag de imagem, por padrão, a imagem identificada como `latest` será puxada.</p>
  <p>**Atenção**: evite usar a tag mais recente para cargas de trabalho de produção. Você pode não ter testado sua carga de trabalho com a imagem mais recente se estiver usando um repositório público ou compartilhado, como o Docker Hub ou o {{site.data.keyword.registryshort_notm}}.</p>
  <p>Por exemplo, para listar as tags de imagens públicas da IBM:</p>
  <ol><li>Alterne para a região de registro global.<pre class="pre"><code>ibmcloud cr region-set global
</code></pre></li>
  <li>Liste as imagens IBM.<pre class="pre"><code> Imagens do ibmcloud cr -- include-ibm </code></pre></li></ol>
  <p>O `imagePullPolicy` padrão é configurado como `IfNotPresent`, que puxará a imagem somente se ela já não existir localmente. Se desejar que a imagem seja puxada toda vez que o contêiner for iniciado, especifique o `imagePullPolicy: Always`.</p>
  <p><pre class="codeblock"><code> contêineres:
- name: wasliberty
  image: registry.bluemix.net/ibmliberty:webProfile8
  imagePullPolicy: Always</pre></code></p></dd>

<dt id="port">Porta para o serviço do app</dt>
  <dd><p>Selecione uma porta de contêiner na qual abrir os serviços do app. Para ver qual porta precisa ser aberta, consulte suas especificações de app ou o Dockerfile. A porta é acessível por meio da rede privada, mas não de uma conexão de rede pública. Para expor o app publicamente, deve-se criar um serviço NodePort, balanceador de carga ou Ingress. Use esse mesmo número da porta ao [criar um objeto `Service`](#service).</p>
  <p><pre class="codeblock"><code> portas:
- containerPort: 9080</pre></code></p></dd>

<dt id="resourcereq">Solicitações de recurso e limites</dt>
  <dd><p>Como um administrador de cluster, é possível certificar-se de que as equipes que compartilham um cluster não ocupem mais do que seu compartilhamento justo de recursos de cálculo (memória e CPU) criando um [objeto ResourceQuota ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/policy/resource-quotas/) para cada namespace do Kubernetes no cluster. Se o administrador de cluster configurar uma cota de recurso de cálculo, cada contêiner dentro do modelo de implementação deverá especificar solicitações de recurso e limites para memória e CPU, caso contrário, a criação do pod falhará.</p>
  <p><ol><li>Verifique se uma cota de recurso está configurada para um namespace.<pre class="pre"><code> kubectl get quota -- namespace= < namespace> </code></pre></li>
  <li>Veja quais são os limites de cota.<pre class="pre"><code> kubectl describe quota < quota_name> -- namespace= < namespace> </code></pre></li></ol></p>
  <p>Mesmo que nenhuma cota de recurso seja configurada, é possível incluir solicitações de recurso e limites em sua implementação para melhorar o gerenciamento de recursos do nó do trabalhador. **Nota**: se um contêiner exceder seu limite, o contêiner poderá ser reiniciado ou falhar. Se um contêiner exceder uma solicitação, seu pod poderá ser despejado se o nó do trabalhador ficar sem esse recurso que foi excedido. Para obter informações de resolução de problemas, veja [Os pods falham repetidamente ao reiniciar ou são removidos inesperadamente](cs_troubleshoot_clusters.html#pods_fail).</p>
  <p>**Solicitação**: a quantia mínima do recurso que o planejador reserva para que o contêiner use. Se a quantia for igual ao limite, a solicitação será garantida. Se a quantia for menor que o limite, a solicitação ainda será garantida, mas o planejador poderá usar a diferença entre a solicitação e o limite para preencher os recursos de outros contêineres.</p>
  <p>**Limite**: a quantia máxima do recurso que o contêiner pode consumir. Se a quantia total de recursos que for usada nos contêineres exceder a quantia disponível no nó do trabalhador, os contêineres poderão ser despejados para liberar espaço. Para evitar o despejo, configure a solicitação de recurso igual ao limite do contêiner. Se nenhum limite for especificado, o padrão será a capacidade do nó do trabalhador.</p>
  <p>Para obter mais informações, veja a [documentação do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/).</p>
  <p><pre class="codeblock"><code> recursos:
  requests:
    memory: "512Mi"
    cpu: "500m"
  limits:
    memória: "1024Mi"
    cpu: "1000m"</pre></code></p></dd>

<dt id="probe">Análises e sondas de prontidão</dt>
  <dd><p>Por padrão, o Kubernetes envia o tráfego para seus pods de app após todos os contêineres no pod serem iniciados e reinicia os contêineres quando eles travam. No entanto, é possível configurar verificações de funcionamento para melhorar a robustez do roteamento de tráfego de serviço. Por exemplo, seu app pode ter um atraso de inicialização. Os processos de app podem ser iniciados antes que o app inteiro esteja completamente pronto, o que pode afetar as respostas especialmente ao escalar para cima em muitas instâncias. Com verificações de funcionamento, é possível permitir que seu sistema possa saber se o seu app está em execução e pronto para receber solicitações. Configurando essas análises, é possível também ajudar a evitar o tempo de inatividade ao executar uma [atualização contínua](#app_rolling) de seu app. É possível configurar dois tipos de verificações de funcionamento: análises de vivacidade e de prontidão.</p>
  <p>**Análise de vivacidade**: configure uma análise de vivacidade para verificar se o contêiner está em execução. Se a análise falha, o contêiner é reiniciado. Se o contêiner não especifica uma análise de vivacidade, a análise é bem-sucedida porque presume que o contêiner está ativo quando o contêiner está em um status **Em execução**.</p>
  <p>**Análise de prontidão**: configure uma análise de prontidão para verificar se o contêiner está pronto para receber solicitações e tráfego externo. Se a análise falhar, o endereço IP do pod será removido como um endereço IP utilizável para serviços que correspondem ao pod, mas o contêiner não será reiniciado. A configuração de uma análise de prontidão com um atraso inicial é especialmente importante se o seu app leva um tempo para ser inicializado. Antes do atraso inicial, a análise não é iniciada, fornecendo ao seu contêiner um tempo para ficar ativo. Se o contêiner não fornece uma análise de prontidão, a análise é bem-sucedida porque presume que o contêiner está ativo quando o contêiner está em um status **Em execução**.</p>
  <p>É possível configurar as análises como comandos, solicitações de HTTP ou soquetes TCP. O exemplo usa solicitações de HTTP. Dê ao sensor de vivacidade mais tempo do que à análise de prontidão. Para obter mais informações, veja a [documentação do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).</p>
  <p><pre class="codeblock"><code> livenessProbe:
  httpGet:
    path: /
    port: 9080
  initialDelaySeconds: 300
  periodSeconds: 15
readinessProbe:
  httpGet:
    path: /
    port: 9080
  initialDelaySeconds: 45
  periodSeconds: 5</pre></code></p></dd>

<dt id="service">Expondo o serviço de app</dt>
  <dd><p>É possível criar um serviço que exponha seu app. Na seção `spec`, certifique-se de corresponder os valores de `port` e de rótulo aos que você usou na implementação. O serviço expõe objetos que correspondem ao rótulo, tal como `app: wasliberty` no exemplo a seguir.</p>
  <ul><li>Por padrão, um serviço usa [ClusterIP ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/), que torna o serviço acessível somente dentro do cluster, mas não fora do cluster.</li>
  <li>É possível criar um serviço NodePort, balanceador de carga ou Ingress para expor o app publicamente. Esses serviços têm dois IPs, um externo e um interno. Quando o tráfego é recebido no IP externo, ele é encaminhado para o IP do cluster interno. Em seguida, por meio do IP do cluster interno, o tráfego é roteado para o IP do contêiner do app.</li>
  <li>O exemplo usa `NodePort` para expor o serviço fora do cluster. Para obter informações sobre como configurar o acesso externo, veja [Escolhendo um serviço NodePort, balanceador de carga ou Ingress](cs_network_planning.html#external).</li></ul>
  <p><pre class="codeblock"><code>apiVersion: v1
kind: Service
metadados:
  name: wasliberty
  labels:
    app: wasliberty
spec:
  ports:
  - port: 9080
  selector:
    app: wasliberty
  type: NodePort</pre></code></p></dd>

<dt id="configmap">Configmaps para variáveis de ambiente de contêiner</dt>
<dd><p>Os configmaps fornecem informações de configuração não confidenciais para suas cargas de trabalho de implementação. O exemplo a seguir mostra como é possível referenciar valores de seu configmap como variáveis de ambiente na seção de especificação de contêiner de seu YAML de implementação. Referenciando valores de seu configmap, é possível desacoplar essas informações de configuração de sua implementação para manter seu app conteinerizado móvel.<ul><li>[Ajude-me a decidir se deve ser usado um objeto do Kubernetes `ConfigMap` ou `Secret` para variáveis](#variables).</li>
<li>Para obter mais maneiras de usar configmaps, veja a [documentação do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/).</li></ul></p>
<p><pre class="codeblock"><code> apiVersion: apps / v1beta1
Tipo: implementação
metadados:
  name: wasliberty
spec:
  replicas: 3
  :
    ...
    spec:
      ...
      containers:
      - name: wasliberty ...
        env:
          - name: VERSION
            valueFrom:
              configMapKeyRef:
                name: wasliberty
                key: VERSION
          - name: LANGUAGE
            valueFrom:
              configMapKeyRef:
                name: wasliberty
                key: LANGUAGE
        ...
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: wasliberty
  labels:
    app: wasliberty
data:
  VERSION: "1.0"
  LANGUAGE: en</pre></code></p></dd>

  <dt id="secret">Segredos para variáveis de ambiente de contêiner</dt>
  <dd><p>Os segredos fornecem informações confidenciais de configuração, como senhas para suas cargas de trabalho de implementação. O exemplo a seguir mostra como é possível referenciar valores de seu segredo como variáveis de ambiente na seção de especificação de contêiner de seu YAML de implementação. Também é possível montar o segredo como um volume. Referenciando valores de seu segredo, é possível desacoplar essas informações de configuração de sua implementação para manter seu app conteinerizado móvel.<ul><li>[Ajude-me a decidir se deve ser usado um ConfigMap ou Secret para variáveis](#variables).</li>
  <li>Para obter mais informações, veja [Entendendo quando usar segredos](cs_encrypt.html#secrets).</li></ul></p>
  <p><pre class="codeblock"><code> apiVersion: apps / v1beta1
  Tipo: implementação
  metadados:
    name: wasliberty
  spec:
    replicas: 3
    :
      ...
      spec:
        ...
        containers:
        - name: wasliberty ...
          env:
          - name: username
            valueFrom:
              secretKeyRef:
                name: wasliberty
                key: username
          - name: password
            valueFrom:
              secretKeyRef:
                name: wasliberty
                key: password
          ...
  ---
  apiVersion: v1
  kind: Secret
  metadata:
    name: wasliberty
    labels:
      app: wasliberty
  type: Opaque
  data:
    username: dXNlcm5hbWU=
    password: cGFzc3dvcmQ=</pre></code></p></dd>

<dt id="pv">Volumes Persistentes para Armazenamento de Contêiner</dt>
<dd><p>Os volumes persistentes (PVs) fazem interface com o armazenamento físico para fornecer armazenamento de dados persistentes para as cargas de trabalho do contêiner. O exemplo a seguir mostra como é possível incluir armazenamento persistente em seu app. Para provisionar o armazenamento persistente, crie uma solicitação de volume persistente (PVC) para descrever o tipo e o tamanho do armazenamento de arquivo que você deseja ter. Depois de criar o PVC, o volume persistente e o armazenamento físico são criados automaticamente usando o [fornecimento dinâmico](cs_storage_basics.html#dynamic_provisioning). Referenciando o PVC em seu YAML de implementação, o armazenamento é montado automaticamente em seu pod de app. Quando o contêiner em seu pod grava dados no diretório de caminho de montagem `/test`, os dados são armazenados na instância de armazenamento de arquivo NFS.</p><ul><li>Para obter mais informações, veja [Entendendo os fundamentos básicos do armazenamento do Kubernetes](cs_storage_basics.html#kube_concepts).</li><li>Para obter opções sobre outros tipos de armazenamento que é possível provisionar, veja [Planejando o armazenamento persistente altamente disponível](cs_storage_planning.html#storage_planning).</li></ul>
<p><pre class="codeblock"><code> apiVersion: apps / v1beta1
Tipo: implementação
metadados:
  name: wasliberty
spec:
  replicas: 3
  :
    ...
    spec:
      ...
      containers:
      - name: wasliberty ...
        volumeMounts:
        - name: pvmount
          mountPath: /test
      volumes:
      - name: pvmount
        persistentVolumeClaim:
          claimName: wasliberty
        ...
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wasliberty
  annotations:
    volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
  labels:
    billingType: "hourly"
    app: wasliberty
spec:
  accessModes:
    - ReadWriteMany
         resources:
           requests:
             storage: 24Gi</pre></code></p></dd>

<dt id="nextsteps">Pronto para implementar um app?</dt>
<dd><ul><li>[Use uma cópia do YAML completo como um modelo para iniciar](https://raw.githubusercontent.com/IBM-Cloud/kube-samples/master/deploy-apps-clusters/deploy_wasliberty.yaml).</li>
<li>[Implemente um app por meio do painel do Kubernetes](cs_app.html#app_ui).</li>
<li>[ Implementar um app a partir da CLI ](cs_app.html#app_cli).</li></ul></dd>

</dl>

### YAML de implementação de exemplo completo
{: #yaml-example}

A seguir está uma cópia do YAML de implementação que foi [discutido seção por seção anteriormente](#app_yaml). Também é possível [fazer download do YAML por meio do GitHub](https://raw.githubusercontent.com/IBM-Cloud/kube-samples/master/deploy-apps-clusters/deploy_wasliberty.yaml).

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: wasliberty
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: wasliberty
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - wasliberty
              topologyKey: kubernetes.io/hostname
      containers:
      - name: wasliberty
        image: registry.bluemix.net/ibmliberty
        env:
          - name: VERSION
            valueFrom:
              configMapKeyRef:
                name: wasliberty
                key: VERSION
          - name: LANGUAGE
            valueFrom:
              configMapKeyRef:
                name: wasliberty
                key: LANGUAGE
          - name: username
            valueFrom:
              secretKeyRef:
                name: wasliberty
                key: username
          - name: password
            valueFrom:
              secretKeyRef:
                name: wasliberty
                key: password
        ports:
          - containerPort: 9080
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "1024Mi"
            cpu: "1000m"
        livenessProbe:
          httpGet:
            path: /
            port: 9080
          initialDelaySeconds: 300
          periodSeconds: 15
        readinessProbe:
          httpGet:
            path: /
            port: 9080
          initialDelaySeconds: 45
          periodSeconds: 5
        volumeMounts:
        - name: pvmount
          mountPath: /test
      volumes:
      - name: pvmount
        persistentVolumeClaim:
          claimName: wasliberty
---
apiVersion: v1
kind: Service
metadata:
  name: wasliberty
  labels:
    app: wasliberty
spec:
  ports:
  - port: 9080
  selector:
    app: wasliberty
  type: NodePort
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: wasliberty
  labels:
    app: wasliberty
data:
  VERSION: "1.0"
  LANGUAGE: en
---
apiVersion: v1
kind: Secret
metadata:
  name: wasliberty
  labels:
    app: wasliberty
type: Opaque
data:
  username: dXNlcm5hbWU=
  password: cGFzc3dvcmQ=
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wasliberty
  annotations:
    volume.beta.kubernetes.io/storage-class: "ibmc-file-bronze"
  labels:
    billingType: "hourly"
    app: wasliberty
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 24Gi
```
{: codeblock}

<br />


## Ativando o painel do Kubernetes
{: #cli_dashboard}

Abra um painel do Kubernetes em seu sistema local para visualizar informações sobre um cluster e seus nós do trabalhador. [Na GUI](#db_gui), é possível acessar o painel com um conveniente botão de um clique. [Com a CLI](#db_cli), é possível acessar o painel ou usar as etapas em um processo de automação, como para um pipeline CI/CD.
{:shortdesc}

Antes de iniciar: [Efetue login em sua conta. Destine a região apropriada e, se aplicável, o grupo de recursos. Configure o contexto para seu cluster](cs_cli_install.html#cs_cli_configure).

É possível usar a porta padrão ou configurar sua própria porta para ativar o painel do Kubernetes para um cluster.

**Ativando o painel do Kubernetes por meio da GUI**
{: #db_gui}

1.  Efetue login no [{{site.data.keyword.Bluemix_notm}} GUI](https://console.bluemix.net/).
2.  No seu perfil na barra de menus, selecione a conta que você deseja usar.
3.  No menu, clique em **Contêineres**.
4.  Na página **Clusters**, clique no cluster que você deseja acessar.
5.  Na página de detalhes do cluster, clique no botão **Painel do Kubernetes**.

</br>
</br>

**Ativando o painel do Kubernetes por meio da CLI**
{: #db_cli}

1.  Obtenha suas credenciais para o Kubernetes.

    ```
    kubectl config view -o jsonpath='{.users[0].user.auth-provider.config.id-token}'
    ```
    {: pre}

2.  Copie o valor **id-token** que é mostrado na saída.

3.  Configure o proxy com o número da porta padrão.

    ```
    kubectl proxy
    ```
    {: pre}

    Saída de exemplo:

    ```
    Iniciando a entrega em 127.0.0.1:8001
    ```
    {: screen}

4.  Conecte-se ao painel.

  1.  Em seu navegador, navegue para a URL a seguir:

      ```
      http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
      ```
      {: codeblock}

  2.  Na página de conexão, selecione o método de autenticação **Token**.

  3.  Em seguida, cole o valor **id-token** que você copiou anteriormente no campo **Token** e clique em **CONECTAR**.

Quando estiver pronto com o painel do Kubernetes, use `CTRL+C` para sair do comando `proxy`. Depois de sair, o painel do Kubernetes não estará mais disponível. Execute o comando `proxy` para reiniciar o painel do Kubernetes.

[Em seguida, é possível executar um arquivo de configuração do painel.](#app_ui)

<br />


## Implementando apps com a GUI
{: #app_ui}

Ao implementar um app em seu cluster usando o painel do Kubernetes, um recurso de implementação cria, atualiza e gerencia automaticamente os pods em seu cluster.
{:shortdesc}

Antes de iniciar:

-   [ Instale as CLIs necessárias ](cs_cli_install.html#cs_cli_install).
-   [Efetue login em sua conta. Destine a região apropriada e, se aplicável, o grupo de recursos. Configure o contexto para seu cluster](cs_cli_install.html#cs_cli_configure).

Para implementar seu app:

1.  Abra o [painel](#cli_dashboard) do Kubernetes e clique em **+ Criar**.
2.  Insira os detalhes do app em uma de duas maneiras.
  * Selecione **Especificar detalhes do app abaixo** e insira os detalhes.
  * Selecione **Fazer upload de um arquivo YAML ou JSON** para fazer upload do [arquivo de configuração de seu app ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/).

  Precisa de ajuda com seu arquivo de configuração. Verifique este [arquivo YAML de exemplo ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/IBM-Cloud/kube-samples/blob/master/deploy-apps-clusters/deploy-ibmliberty.yaml). Neste exemplo, um contêiner é implementado por meio da imagem **ibmliberty** na região sul dos EUA. Saiba mais sobre [como proteger suas informações pessoais](cs_secure.html#pi) quando trabalhar com recursos do Kubernetes.
  {: tip}

3.  Verifique se você implementou com sucesso o seu app em uma das maneiras a seguir.
  * No painel do Kubernetes, clique em **Implementações**. Uma lista de implementações bem-sucedidas é exibida.
  * Se o seu app estiver [publicamente disponível](cs_network_planning.html#public_access), navegue para a página de visão geral do cluster no painel do {{site.data.keyword.containerlong}}. Copie o subdomínio, que está localizado na seção de resumo do cluster e cole-o em um navegador para visualizar seu app.

<br />


## Implementando apps com a CLI
{: #app_cli}

Após um cluster ser criado, é possível implementar um app nesse cluster usando a CLI do Kubernetes.
{:shortdesc}

Antes de iniciar:

-   Instale as [CLIs](cs_cli_install.html#cs_cli_install) necessárias.
-   [Efetue login em sua conta. Destine a região apropriada e, se aplicável, o grupo de recursos. Configure o contexto para seu cluster](cs_cli_install.html#cs_cli_configure).

Para implementar seu app:

1.  Crie um arquivo de configuração com base nas [melhores práticas do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/overview/). Geralmente, um arquivo de configuração contém detalhes de configuração para cada um dos recursos que você estiver criando no Kubernetes. Seu script pode incluir uma ou mais das seções a seguir:

    -   [Implementação ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/): define a criação de pods e conjuntos de réplicas. Um pod inclui um app conteinerizado individual e os conjuntos de réplicas controlam múltiplas instâncias de pods.

    -   [Serviço ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/services-networking/service/): fornece acesso de front-end para os pods usando um nó do trabalhador ou um endereço IP público do balanceador de carga ou uma rota pública do Ingresso.

    -   [Ingresso ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/services-networking/ingress/): especifica um tipo de balanceador de carga que fornece rotas para acessar seu app publicamente.

    Saiba mais sobre [como proteger suas informações pessoais](cs_secure.html#pi) quando trabalhar com recursos do Kubernetes.

2.  Execute o arquivo de configuração no contexto de um cluster.

    ```
    Kubectl apply -f config.yaml
    ```
    {: pre}

3.  Se você disponibilizou o seu app publicamente usando um serviço de porta de nó, um serviço de balanceador de carga ou Ingresso, verifique se é possível acessar o app.

<br />


## Implementando apps em nós do trabalhador específicos usando rótulos
{: #node_affinity}

Ao implementar um app, os pods de app são implementados indiscriminadamente em vários nós do trabalhador em seu cluster. Em alguns casos, você pode desejar restringir os nós do trabalhador nos quais os pods de app são implementados. Por exemplo, você pode desejar que os pods de app sejam implementados somente em nós do trabalhador em um determinado conjunto de trabalhadores porque esses nós do trabalhador estão em máquinas bare metal. Para designar os nós do trabalhador nos quais os pods de app devem ser implementados, inclua uma regra de afinidade em sua implementação de app.
{:shortdesc}

Antes de iniciar: [Efetue login em sua conta. Destine a região apropriada e, se aplicável, o grupo de recursos. Configure o contexto para seu cluster](cs_cli_install.html#cs_cli_configure).

1. Obtenha o nome do conjunto de trabalhadores no qual você deseja implementar os pods de app.
    ```
    ibmcloud ks worker-pools <cluster_name_or_ID>
    ```
    {:pre}

    Essas etapas usam um nome do conjunto de trabalhadores como um exemplo. Para implementar os pods de app em determinados nós do trabalhador com base em outro fator, obtenha esse valor no lugar. Por exemplo, para implementar pods de app somente em nós do trabalhador em uma VLAN específica, obterá o ID de VLAN executando `ibmcloud ks vlans <zone>`.
    {: tip}

2. [Inclua uma regra de afinidade ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#node-affinity-beta-feature) para o nome do conjunto de trabalhos para a implementação de app.

    Exemplo de yaml:

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: with-node-affinity
    spec:
      template:
        spec:
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: workerPool
                    operator: In
                    values:
                    - < worker_pool_name>...
    ```
    {: codeblock}

    Na seção **affinity** do exemplo yaml, `workerPool` é a `key` e `<worker_pool_name>` é o `value`.

3. Aplique o arquivo de configuração de implementação atualizado.
    ```
    Kubectl apply -f com-node-affinity.yaml
    ```
    {: pre}

4. Verifique se os pods de app foram implementados nos nós do trabalhador corretos.

    1. Liste os pods em seu cluster.
        ```
        kubectl get pods -o wide
        ```
        {: pre}

        Saída de exemplo:
        ```
        NAME                   READY     STATUS              RESTARTS   AGE       IP               NODE
        cf-py-d7b7d94db-vp8pq  1/1       Running             0          15d       172.30.xxx.xxx   10.176.48.78
        ```
        {: screen}

    2. Na saída, identifique um pod para seu app. Anote o endereço IP privado do **NODE** do nó do trabalhador no qual o pod está.

        Na saída de exemplo acima, o pod de app `cf-py-d7b7d94db-vp8pq` está em um nó do trabalhador com o endereço IP `10.176.48.78`.

    3. Liste os nós do trabalhador no conjunto de trabalhadores que você designou em sua implementação de app.

        ```
        ibmcloud ks workers <cluster_name_or_ID> --worker-pool <worker_pool_name>
        ```
        {: pre}

        Saída de exemplo:

        ```
        ID                                                 Public IP       Private IP     Machine Type      State    Status  Zone    Version
        kube-dal10-crb20b637238bb471f8b4b8b881bbb4962-w7   169.xx.xxx.xxx  10.176.48.78   b2c.4x16          normal   Ready   dal10   1.8.6_1504
        kube-dal10-crb20b637238bb471f8b4b8b881bbb4962-w8   169.xx.xxx.xxx  10.176.48.83   b2c.4x16          normal   Ready   dal10   1.8.6_1504
        kube-dal12-crb20b637238bb471f8b4b8b881bbb4962-w9   169.xx.xxx.xxx  10.176.48.69   b2c.4x16          normal   Ready   dal12   1.8.6_1504
        ```
        {: screen}

        Se você criou uma regra de afinidade de app baseada em outro fator, obtenha esse valor no lugar. Por exemplo, para verificar se o pod de app foi implementado em um nó do trabalhador em uma VLAN específica, visualize a VLAN em que o nó do trabalhador está executando `ibmcloud ks worker-get <cluster_name_or_ID> <worker_ID>`.
        {: tip}

    4. Na saída, verifique se o nó do trabalhador com o endereço IP privado que você identificou na etapa anterior está implementado nesse conjunto de trabalhadores.

<br />


## Implementando um app em uma máquina de GPU
{: #gpu_app}

Se você tem um [tipo de máquina de unidade de processamento de gráfico (GPU) bare metal](cs_clusters_planning.html#shared_dedicated_node), é possível planejar cargas de trabalho matematicamente intensivas no nó do trabalhador. Por exemplo, você pode executar um app 3D que usa a plataforma Compute Unified Device Architecture (CUDA) para compartilhar a carga de processamento da GPU e CPU para aumentar o desempenho.
{:shortdesc}

Nas etapas a seguir, você aprenderá como implementar cargas de trabalho que requerem a GPU. Também é possível [implementar apps](#app_ui) que não precisam processar suas cargas de trabalho na GPU e CPU. Depois, você pode achar útil experimentar cargas de trabalho matematicamente intensivas, como a estrutura de aprendizado de máquina [TensorFlow ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://www.tensorflow.org/) com [esta demo do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://github.com/pachyderm/pachyderm/tree/master/doc/examples/ml/tensorflow).

Antes de iniciar:
* [Crie um tipo de máquina de GPU bare metal](cs_clusters.html#clusters_cli). Observe que esse processo pode levar mais de 1 dia útil para ser concluído.
* Seu cluster mestre e o nó do trabalhador de GPU devem executar o Kubernetes versão 1.10 ou mais recente.

Para executar uma carga de trabalho em uma máquina de GPU:
1.  Crie um arquivo YAML. Neste exemplo, um `Job` YAML gerencia cargas de trabalho em lote, fazendo um pod de curta duração que é executado até que o comando planejado para ser concluído com êxito seja finalizado.

    **Importante**: para cargas de trabalho de GPU, deve-se sempre fornecer o campo `resources: limits: nvidia.com/gpu` na especificação YAML.

    ```yaml
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: nvidia-smi
      labels:
        name: nvidia-smi
    spec:
      template:
        metadata:
          labels:
            name: nvidia-smi
        spec:
          containers:
          - name: nvidia-smi
            image: nvidia/cuda:9.1-base-ubuntu16.04
            command: [ "/usr/test/nvidia-smi" ]
            imagePullPolicy: IfNotPresent
            resources:
              limits:
                nvidia.com/gpu: 2
            volumeMounts:
            - mountPath: /usr/test
              name: nvidia0
          volumes:
            - name: nvidia0
              hostPath:
                path: /usr/bin
          restartPolicy: Never
    ```
    {: codeblock}

    <table>
    <caption>Componentes do YAML</caption>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="Ícone de ideia"/> entendendo os componentes de arquivo do YAML</th>
    </thead>
    <tbody>
    <tr>
    <td>Metadados e nomes de rótulo</td>
    <td>Dê um nome e um rótulo para a tarefa e use o mesmo nome nos metadados do arquivo e nos metadados de `spec template`. Por exemplo, `nvidia-smi`.</td>
    </tr>
    <tr>
    <td><code> containers.image </code></td>
    <td>Forneça a imagem da qual o contêiner é uma instância em execução. Neste exemplo, o valor é configurado para usar a imagem do DockerHub CUDA:<code>nvidia/cuda:9.1-base-ubuntu16.04</code></td>
    </tr>
    <tr>
    <td><code> containers.command </code></td>
    <td>Especifique um comando para ser executado no contêiner. Neste exemplo, o comando <code>[ "/usr/test/nvidia-smi" ]</code>refere-se a um binário que está na máquina de GPU; portanto, deve-se também configurar uma montagem do volume.</td>
    </tr>
    <tr>
    <td><code> containers.imagePullPolicy </code></td>
    <td>Para puxar uma nova imagem somente se a imagem não estiver atualmente no nó do trabalhador, especifique <code>IfNotPresent</code>.</td>
    </tr>
    <tr>
    <td><code> resources.limits </code></td>
    <td>Para máquinas de GPU, deve-se especificar o limite de recurso. O [Plug-in do dispositivo![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/cluster-administration/device-plugins/) do Kubernetes configura a solicitação de recurso padrão para corresponder ao limite.
    <ul><li>Deve-se especificar a chave como <code>nvidia.com/gpu</code>.</li>
    <li>Insira o número inteiro de GPUs que você solicitar, como <code>2</code>. <strong>Nota</strong>: os pods do contêiner não compartilham GPUs e as GPUs não podem estar supercomprometidas. Por exemplo, se você tem somente 1 máquina `mg1c.16x128`, então tem apenas 2 GPUs nessa máquina e pode especificar um máximo de `2`.</li></ul></td>
    </tr>
    <tr>
    <td><code>volumeMounts</code></td>
    <td>Nomeie o volume que está montado no contêiner, como <code>nvidia0</code>. Especifique o <code>mountPath</code> no contêiner para o volume. Neste exemplo, o caminho <code>/usr/test</code> corresponde ao caminho usado no comando de contêiner de tarefa.</td>
    </tr>
    <tr>
    <td><code>volumes</code></td>
    <td>Nomeie o volume de tarefa, como <code>nvidia0</code>. No <code>hostPath</code> do nó do trabalhador da GPU, especifique o <code>path</code> do volume no host, neste exemplo, <code>/usr/bin</code>. O contêiner <code>mountPath</code> é mapeado para o <code>path</code> do volume do host, que fornece esse acesso de tarefa aos binários do NVIDIA no nó do trabalhador da GPU para o comando de contêiner a ser executado.</td>
    </tr>
    </tbody></table>

2.  Aplique o arquivo YAML. Por exemplo:

    ```
    Kubectl apply -f nvidia-smi.yaml
    ```
    {: pre}

3.  Verifique o pod de tarefa filtrando os pods pelo rótulo `nvidia-sim`. Verifique se o **STATUS** é **Concluído**.

    ```
    kubectl get pod -a -l 'name in (nvidia-sim)'
    ```
    {: pre}

    Saída de exemplo:
    ```
    NAME                  READY     STATUS      RESTARTS   AGE
    nvidia-smi-ppkd4      0/1       Completed   0          36s
    ```
    {: screen}

4.  Descreva o pod para ver como o plug-in do dispositivo de GPU planejou o pod.
    * Nos campos `Limits` e `Requests`, veja se o limite de recurso que você especificou corresponde à solicitação que o plug-in do dispositivo configurou automaticamente.
    * Nos eventos, verifique se o pod está designado ao seu nó do trabalhador da GPU.

    ```
    Kubectl describe pod nvidia-smi-ppkd4
    ```
    {: pre}

    Saída de exemplo:
    ```
    Name:           nvidia-smi-ppkd4
    Namespace:      default
    ...
    Limits:
     nvidia.com/gpu:  2
    Requests:
     nvidia.com/gpu:  2
    ...
    Events:
    Type    Reason                 Age   From                     Message
    ----    ------                 ----  ----                     -------
    Normal  Scheduled              1m    default-scheduler        Successfully assigned nvidia-smi-ppkd4 to 10.xxx.xx.xxx
    ...
    ```
    {: screen}

5.  Para verificar se a tarefa usou a GPU para calcular sua carga de trabalho, é possível verificar os logs. O comando `[ "/usr/test/nvidia-smi" ]` da tarefa consultou o estado do dispositivo GPU no nó do trabalhador da GPU.

    ```
    Kubectl logs nvidia a simulação ppkd4
    ```
    {: pre}

    Saída de exemplo:
    ```
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 390.12                 Driver Version: 390.12                    |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla K80           Off  | 00000000:83:00.0 Off |                  Off |
    | N/A   37C    P0    57W / 149W |      0MiB / 12206MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   1  Tesla K80           Off  | 00000000:84:00.0 Off |                  Off |
    | N/A   32C    P0    63W / 149W |      0MiB / 12206MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+

    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```
    {: screen}

    Neste exemplo, você vê que ambas as GPUs foram usadas para executar a tarefa porque foram planejadas no nó do trabalhador. Se o limite for configurado como 1, somente 1 GPU será mostrada.

## Ajuste de escala de apps
{: #app_scaling}

Com o Kubernetes, é possível ativar o [ajuste automático de escala de pod horizontal ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) para aumentar ou diminuir automaticamente o número de instâncias de seus apps com base na CPU.
{:shortdesc}

Procurando informações sobre ajuste de escala de aplicativos Cloud Foundry? Confira [IBM Auto-Scaling for {{site.data.keyword.Bluemix_notm}}](/docs/services/Auto-Scaling/index.html). 
{: tip}

Antes de iniciar:
- [Efetue login em sua conta. Destine a região apropriada e, se aplicável, o grupo de recursos. Configure o contexto para seu cluster](cs_cli_install.html#cs_cli_configure).
- O monitoramento Heapster deve ser implementado no cluster em que você deseja ajustar a escala automaticamente.

Etapas:

1.  Implemente seu app em um cluster por meio da CLI. Ao implementar seu app, deve-se solicitar a CPU.

    ```
    kubectl run <app_name> --image=<image> --requests=cpu=<cpu> --expose --port=<port_number>
    ```
    {: pre}

    <table>
    <caption>Componentes de comando para executar kubectl</caption>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="Ícone de ideia"/> entendendo os componentes desse comando</th>
    </thead>
    <tbody>
    <tr>
    <td><code>--image</code></td>
    <td>O aplicativo que você deseja implementar.</td>
    </tr>
    <tr>
    <td><code>--request=cpu</code></td>
    <td>A CPU necessária para o contêiner, que é especificada em milinúcleos. Como um exemplo, <code>--requests=200m</code>.</td>
    </tr>
    <tr>
    <td><code>--expose</code></td>
    <td>Quando true, cria um serviço externo.</td>
    </tr>
    <tr>
    <td><code>--port</code></td>
    <td>A porta em que seu app está disponível externamente.</td>
    </tr></tbody></table>

    Para implementações mais complexas, você pode precisar criar um [arquivo de configuração](#app_cli).
    {: tip}

2.  Crie um ajustador automático de escala e defina sua política. Para obter mais informações sobre como trabalhar com o comando `kubectl autoscale`, veja [a documentação do Kubernetes ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://v1-8.docs.kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale).

    ```
    kubectl autoscale deployment <deployment_name> --cpu-percent=<percentage> --min=<min_value> --max=<max_value>
    ```
    {: pre}

    <table>
    <caption>Componentes de comando para kubectl autoscale</caption>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="Ícone de ideia"/> entendendo os componentes desse comando</th>
    </thead>
    <tbody>
    <tr>
    <td><code>--cpu-percent</code></td>
    <td>A utilização média da CPU que é mantida pelo Escalador automático de ajuste de pod horizontal, que é especificada em porcentagem.</td>
    </tr>
    <tr>
    <td><code>--min</code></td>
    <td>O número mínimo de pods implementados que são usados para manter a porcentagem de utilização da CPU especificada.</td>
    </tr>
    <tr>
    <td><code>--max</code></td>
    <td>O número máximo de pods implementados que são usados para manter a porcentagem de utilização da CPU especificada.</td>
    </tr>
    </tbody></table>


<br />


## Gerenciando implementações contínuas para atualizar seus apps
{: #app_rolling}

É possível gerenciar o lançamento de suas mudanças de app em um modo automatizado e controlado para cargas de trabalho com um modelo de pod, como implementações. Se a sua apresentação não estiver indo de acordo com o plano, será possível recuperar a sua implementação para a revisão anterior.
{:shortdesc}

Deseja evitar o tempo de inatividade durante a atualização contínua? Certifique-se de especificar uma [análise de prontidão em sua implementação](#probe) para que o lançamento continue no próximo pod de app após o pod atualizado mais recentemente estar pronto.
{: tip}

Antes de iniciar, crie uma [implementação](#app_cli).

1.  [Apresente ![Ícone de link externo](../icons/launch-glyph.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) uma mudança. Por exemplo, talvez você queira mudar a imagem usada na implementação inicial.

    1.  Obtenha o nome da implementação.

        ```
        kubectl get deployments
        ```
        {: pre}

    2.  Obtenha o nome do pod.

        ```
        kubectl get pods
        ```
        {: pre}

    3.  Obtenha o nome do contêiner que está em execução no pod.

        ```
        kubectl describe pod <pod_name>
        ```
        {: pre}

    4.  Configure a nova imagem para a implementação para usar.

        ```
        kubectl set image deployment/<deployment_name><container_name>=<image_name>
        ```
        {: pre}

    Ao executar os comandos, a mudança é imediatamente aplicada e registrada no histórico de apresentação.

2.  Verifique o status de sua implementação.

    ```
    kubectl rollout status deployments/<deployment_name>
    ```
    {: pre}

3.  Recupere uma mudança.
    1.  Visualize o histórico de apresentação da implementação e identifique o número da revisão de sua última implementação.

        ```
        kubectl rollout history deployment/<deployment_name>
        ```
        {: pre}

        **Dica:** para ver os detalhes de uma revisão específica, inclua o número da revisão.

        ```
        kubectl rollout history deployment/<deployment_name> --revision=<number>
        ```
        {: pre}

    2.  Recupere para a versão anterior ou especifique uma revisão. Para recuperar para a versão anterior, use o comando a seguir.

        ```
        kubectl rollout undo deployment/<depoyment_name> --to-revision=<number>
        ```
        {: pre}

<br />

