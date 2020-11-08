---
title: Come usare Analisi del testo per l'integrità
titleSuffix: Azure Cognitive Services
description: Informazioni su come estrarre ed etichettare le informazioni mediche da testo clinico non strutturato con Analisi del testo per l'integrità.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: e3e0ae444e2b3b6ac195a83653baf4b71bac6644
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363868"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Procedura: usare Analisi del testo per l'integrità (anteprima)

> [!NOTE]
> Il contenitore Analisi del testo per l'integrità è stato aggiornato di recente. Per ulteriori informazioni sulle modifiche recenti [, vedere Novità](../whats-new.md) . Ricordarsi di eseguire il pull del contenitore più recente per usare gli aggiornamenti elencati.

> [!IMPORTANT] 
> Analisi del testo per l'integrità è una funzionalità di anteprima fornita "così com'è" e "con tutti gli errori". Di conseguenza, **analisi del testo per Health (Preview) non devono essere implementate o distribuite in alcun uso in produzione.** Analisi del testo per l'integrità non è intenzionale o reso disponibile per l'utilizzo come dispositivo medico, supporto clinico, strumento di diagnostica o altra tecnologia da utilizzare per la diagnosi, la cura, la mitigazione, il trattamento o la prevenzione di patologie o altre condizioni, né la concessione di alcuna licenza o diritto da parte di Microsoft di utilizzare questa funzionalità per tali scopi. Questa funzionalità non è progettata o progettata per essere implementata o distribuita come sostituto di consigli medici professionali o di opinione sanitaria, diagnosi, trattamento o giudizio clinico di un professionista sanitario e non deve essere usato come tali. Il cliente è esclusivamente responsabile dell'utilizzo di Analisi del testo per l'integrità. Microsoft non garantisce che Analisi del testo per l'integrità o i materiali forniti in relazione alla funzionalità saranno sufficienti per qualsiasi scopo medico o per soddisfare i requisiti sanitari o medici di qualsiasi persona. 


Analisi del testo per l'integrità è un servizio in contenitori che estrae ed etichetta le informazioni mediche rilevanti da testi non strutturati, come le note del medico, i riepiloghi di scaricamento, i documenti clinici e i record di integrità elettronici.  

## <a name="features"></a>Funzionalità

Il Analisi del testo per il contenitore di integrità esegue attualmente il riconoscimento delle entità denominate (NER), l'estrazione della relazione, la negazione dell'entità e il collegamento di entità per testo in lingua inglese nel proprio ambiente di sviluppo che soddisfa i requisiti specifici di sicurezza e governance dei dati.

#### <a name="named-entity-recognition"></a>[Riconoscimento di entità denominate](#tab/ner)

Il riconoscimento delle entità denominato rileva le parole e le frasi indicate nel testo non strutturato che possono essere associate a uno o più tipi semantici, ad esempio la diagnosi, il nome del farmaco, il sintomo/segno o l'età.

> [!div class="mx-imgBorder"]
> ![NER integrità](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Estrazione di relazioni](#tab/relation-extraction)

L'estrazione di relazioni identifica connessioni significative tra i concetti indicati nel testo. Ad esempio, una relazione "ora della condizione" viene rilevata associando il nome di una condizione a un'ora. 

> [!div class="mx-imgBorder"]
> ![Integrità RE](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Collegamento delle entità](#tab/entity-linking)

Il collegamento di entità ambiguità entità distinte associando entità denominate indicate in testo ai concetti trovati in un database di concetti predefiniti. Ad esempio, Unified Medical Language System (UMLS).

> [!div class="mx-imgBorder"]
> ![EL di stato](../media/ta-for-health/health-entity-linking.png)

Analisi del testo per l'integrità supporta il collegamento all'integrità e ai vocabolari biomedici disponibili nella Knowledge base del Metathesaurus[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)(Unified Medical Language System).

#### <a name="negation-detection"></a>[Rilevamento della negazione](#tab/negation-detection) 

Il significato del contenuto medico è molto influenzato dai modificatori, ad esempio la negazione, che può avere una implicazione critica se non viene diagnosticata. Analisi del testo per Health supporta il rilevamento delle negazioni per le diverse entità indicate nel testo. 

> [!div class="mx-imgBorder"]
> ![Stato negativo](../media/ta-for-health/health-negation.png)

---

Vedere le [categorie di entità](../named-entity-types.md?tabs=health) restituite da analisi del testo per l'integrità per un elenco completo delle entità supportate.

## <a name="supported-languages"></a>Lingue supportate

Analisi del testo per l'integrità supporta solo documenti in lingua inglese.

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

Compilare e inviare il [modulo di richiesta di contenitori di servizi cognitivi](https://aka.ms/csgate) per richiedere l'accesso al contenitore. Attualmente non verrà addebitato alcun costo per Analisi del testo per l'utilizzo dell'integrità. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>Installare il contenitore

È possibile installare ed eseguire il contenitore in diversi modi. 

- Usare il [portale di Azure](text-analytics-how-to-install-containers.md?tabs=healthcare) per creare una risorsa analisi del testo e usare Docker per ottenere il contenitore.
- Usare i seguenti script di PowerShell e dell'interfaccia della riga di comando di [Azure](/cli/azure/?view=azure-cli-latest) per automatizzare la configurazione del contenitore di distribuzione

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Installare il contenitore con Azure app Web per contenitori

Azure [app Web per contenitori](https://azure.microsoft.com/services/app-service/containers/) è una risorsa di Azure dedicata all'esecuzione di contenitori nel cloud. Offre funzionalità predefinite, come la scalabilità automatica, il supporto di contenitori Docker e Docker compose, il supporto HTTPS e molto altro ancora.

> [!NOTE]
> Con l'app Web di Azure verrà automaticamente ottenuto un dominio nel formato `<appservice_name>.azurewebsites.net`

Eseguire questo script di PowerShell usando l'interfaccia della riga di comando di Azure per creare una app Web per contenitori, usando la sottoscrizione e l'immagine del contenitore su HTTPS. Attendere il completamento dello script (circa 25-30 minuti) prima di inviare la prima richiesta.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Installare il contenitore usando l'istanza di contenitore di Azure

È anche possibile usare un'istanza di contenitore di Azure (ACI) per semplificare la distribuzione. ACI è una risorsa che consente di eseguire i contenitori Docker su richiesta in un ambiente Azure gestito senza server. 

Vedere [come usare istanze di contenitore di Azure](text-analytics-how-to-use-container-instances.md) per i passaggi per la distribuzione di una risorsa ACI usando il portale di Azure. È anche possibile usare lo script di PowerShell seguente usando l'interfaccia della riga di comando di Azure, che creerà un ACI nella sottoscrizione usando l'immagine del contenitore.  Attendere il completamento dello script (circa 25-30 minuti) prima di inviare la prima richiesta.  A causa del limite per il numero massimo di CPU per ogni risorsa ACI, non selezionare questa opzione se si prevede di inviare più di 5 documenti di grandi dimensioni (circa 5000 caratteri ciascuno) per ogni richiesta.
Per informazioni sulla disponibilità, vedere l'articolo del [supporto tecnico regionale di ACI](../../../container-instances/container-instances-region-availability.md) . 

> [!NOTE] 
> Le istanze di contenitore di Azure non includono il supporto HTTPS per i domini predefiniti. Se è necessario HTTPS, sarà necessario configurarlo manualmente, inclusa la creazione di un certificato e la registrazione di un dominio. È possibile trovare le istruzioni per eseguire questa operazione con NGINX sotto.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Connettività ACI sicura

Per impostazione predefinita, quando si usa ACI con l'API contenitore non viene fornita alcuna sicurezza. Questo perché in genere i contenitori vengono eseguiti come parte di un pod protetto dall'esterno da un Bridge di rete. È tuttavia possibile modificare un contenitore con un componente front-end, mantenendo l'endpoint del contenitore privato. Gli esempi seguenti usano [nginx](https://www.nginx.com) come gateway di ingresso per supportare HTTPS/SSL e l'autenticazione del certificato client.

> [!NOTE]
> NGINX è un proxy e un server HTTP open source a prestazioni elevate. Un contenitore NGINX può essere usato per terminare una connessione TLS per un singolo contenitore. Sono possibili anche le soluzioni di terminazione TLS basate sul traffico in ingresso NGINX più complesse.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Configurare NGINX come gateway di ingresso

NGINX USA [i file di configurazione](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) per abilitare le funzionalità in fase di esecuzione. Per abilitare la terminazione TLS per un altro servizio, è necessario specificare un certificato SSL per terminare la connessione TLS e  `proxy_pass` specificare un indirizzo per il servizio. Di seguito è riportato un esempio.


> [!NOTE]
> `ssl_certificate` prevede che un percorso sia specificato all'interno del file system locale del contenitore NGINX. L'indirizzo specificato per `proxy_pass` deve essere disponibile nella rete del contenitore nginx.

Il contenitore NGINX caricherà tutti i file in `_.conf_` montati nel `/etc/nginx/conf.d/` percorso di configurazione http.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>File Docker compose di esempio

L'esempio seguente illustra come creare un file [Docker compose](https://docs.docker.com/compose/reference/overview) per distribuire NGINX e analisi del testo per i contenitori di integrità:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Per avviare il file Docker compose, eseguire il comando seguente da una console a livello di radice del file:

```bash
docker-compose up
```

Per ulteriori informazioni, vedere la documentazione di NGINX sulla [terminazione di nginx SSL](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).


## <a name="example-api-request"></a>Richiesta API di esempio
Il contenitore fornisce le API dell'endpoint di stima della query basata su REST.  È disponibile anche uno strumento di visualizzazione nel contenitore accessibile aggiungendo la **demo** all'endpoint del contenitore, ad esempio:

```bash
http://<serverURL>:5000/demo
```

Usare la richiesta cURL di esempio riportata di seguito per inviare una query al contenitore distribuito sostituendo la `serverURL` variabile con il valore appropriato.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Il codice JSON seguente è un esempio di file JSON collegato alla Analisi del testo per il corpo POST della richiesta dell'API di integrità:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>Corpo della risposta API

Il codice JSON seguente è un esempio di Analisi del testo per il corpo della risposta dell'API di integrità:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>Output rilevamento negazione

Quando si usa il rilevamento delle negazioni, in alcuni casi un singolo termine di negazione può indirizzare più termini in una sola volta. La negazione di un'entità riconosciuta viene rappresentata nell'output JSON dal valore booleano del `isNegated` flag:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Output estrazione relazione

L'output dell'estrazione della relazione contiene riferimenti URI all' *origine* della relazione e alla relativa *destinazione*. Le entità con ruolo relazione di `ENTITY` vengono assegnate al `target` campo. Le entità con ruolo relazione di `ATTRIBUTE` vengono assegnate al `source` campo. Le relazioni di abbreviazione contengono bidirezionali `source` e `target` campi e `bidirectional` verranno impostate su `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Categorie di entità denominate](../named-entity-types.md)
* [Novità](../whats-new.md)