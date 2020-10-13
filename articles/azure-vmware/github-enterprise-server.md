---
title: Configurare GitHub Enterprise Server nel cloud privato della soluzione VMware di Azure
description: Informazioni su come configurare GitHub Enterprise Server nel cloud privato della soluzione VMware di Azure.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: afce212416c7c12631a7f8d388dc991ed957736f
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949310"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Configurare GitHub Enterprise Server nel cloud privato della soluzione VMware di Azure

Questo articolo illustra i passaggi per configurare GitHub Enterprise Server, la versione "locale" di [github.com](https://github.com/), nel cloud privato della soluzione VMware di Azure. Lo scenario descritto in questa procedura dettagliata è per un'istanza di GitHub Enterprise Server in grado di servire fino a 3.000 sviluppatori che eseguono fino a 25 processi al minuto sulle azioni di GitHub. Include la configurazione delle funzionalità di *Anteprima* (in fase di scrittura), ad esempio le azioni di GitHub. Per personalizzare la configurazione in base alle esigenze specifiche, esaminare i requisiti elencati in [installazione di GitHub Enterprise Server in VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Prima di iniziare

GitHub Enterprise Server richiede un codice di licenza valido. È possibile iscriversi per una [licenza di valutazione](https://enterprise.github.com/trial). Se si desidera estendere le funzionalità di GitHub Enterprise Server tramite un'integrazione, è possibile ottenere una licenza per sviluppatori con cinque postazioni gratuite. Applicare la licenza tramite [il programma partner di GitHub](https://partner.github.com/).

## <a name="installing-github-enterprise-server-on-vmware"></a>Installazione di GitHub Enterprise Server in VMware

Scaricare [la versione corrente di GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) per VMware ESXi/vSphere (OVA) e [distribuire il modello OVA](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) scaricato.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud." (azioni) |
| --- | --- | --- |
| vCPU | 4 | 8 |
| Memoria | 32 GB | 61 GB |
| Archivio collegato | 250 GB | 300 GB |
| Archiviazione radice | 200 GB | 200 GB |

Tuttavia, le esigenze possono variare. Per informazioni sulle considerazioni relative all'hardware, vedere l'articolo relativo all' [installazione di GitHub Enterprise Server in VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Vedere anche [aggiunta di risorse di CPU o di memoria per VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) per personalizzare la configurazione hardware in base alla situazione.

## <a name="configuring-the-github-enterprise-server-instance"></a>Configurazione dell'istanza di GitHub Enterprise Server

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::  

Una volta accesa la macchina virtuale di cui è stato effettuato il provisioning, [configurarla tramite il browser](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Verrà richiesto di caricare il file di licenza e impostare una password della console di gestione. Assicurarsi di annotare la password in un luogo sicuro.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::    

Si consiglia di eseguire almeno i passaggi seguenti:

1. Caricare una chiave SSH pubblica nella console di gestione, in modo che sia possibile [accedere alla shell di amministrazione tramite SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. [Configurare TLS nell'istanza in](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) modo che sia possibile usare un certificato firmato da un'autorità di certificazione attendibile.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Applicare le impostazioni.  Quando l'istanza viene riavviata, è possibile continuare con il passaggio successivo, **configurando l'archiviazione BLOB per le azioni di GitHub**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Dopo il riavvio dell'istanza, creare un nuovo account amministratore nell'istanza di. Assicurarsi di prendere nota anche della password di questo utente.

### <a name="additional-configuration-steps"></a>Passaggi di configurazione aggiuntivi

Per la protezione avanzata dell'istanza per l'uso in produzione, è consigliabile effettuare le seguenti operazioni di configurazione facoltative:

1. Configurare la [disponibilità elevata](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) per la protezione da:

    - Arresti anomali del software (livello del sistema operativo o dell'applicazione)
    - Errori hardware (archiviazione, CPU, RAM e così via)
    - Errori del sistema host di virtualizzazione
    - Rete logicamente o fisicamente grave

2. [Configurare](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) le [utilità di backup](https://github.com/github/backup-utils), fornendo snapshot con versione per il ripristino di emergenza, ospitati in disponibilità separate dall'istanza primaria.
3. [Configurare l'isolamento dei sottodomini](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation), usando un certificato TLS valido, per mitigare gli script tra siti e altre vulnerabilità correlate.

## <a name="configuring-blob-storage-for-github-actions"></a>Configurazione dell'archiviazione BLOB per le azioni di GitHub

> [!NOTE]
> Le azioni di GitHub sono [attualmente disponibili come versione beta limitata su GitHub Enterprise Server versione 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

L'archiviazione BLOB esterna è necessaria per abilitare le azioni di GitHub su GitHub Enterprise Server (attualmente disponibile come funzionalità "beta"). Questo archivio BLOB esterno viene usato dalle azioni per archiviare gli artefatti e i log. Azioni su GitHub Enterprise Server [supporta l'archiviazione BLOB di Azure come provider di archiviazione](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (e altri). Quindi verrà effettuato il provisioning di un nuovo account di archiviazione di Azure con un [tipo di account di archiviazione](../storage/common/storage-account-overview.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json#types-of-storage-accounts) di BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Una volta completata la distribuzione della nuova risorsa BlobStorage, copiare e prendere nota della stringa di connessione (disponibile in chiavi di accesso). Questa stringa sarà necessaria a breve.

## <a name="setting-up-the-github-actions-runner"></a>Configurazione di GitHub actions Runner

> [!NOTE]
> Le azioni di GitHub sono [attualmente disponibili come versione beta limitata su GitHub Enterprise Server versione 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

A questo punto, è necessario disporre di un'istanza di GitHub Enterprise Server in esecuzione con un account amministratore creato. È inoltre necessario disporre di un archivio BLOB esterno che le azioni di GitHub utilizzeranno per la persistenza.

A questo punto è possibile creare un punto per eseguire le azioni di GitHub; anche in questo caso, verrà usata la soluzione VMware di Azure.

Per prima cosa, è necessario eseguire il provisioning di una nuova macchina virtuale nel cluster. La VM verrà basata su [una versione recente di Ubuntu Server](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Dopo aver creato la VM, accenderla e connetterla tramite SSH.

Installare quindi [l'applicazione actions Runner](https://github.com/actions/runner) , che esegue un processo da un flusso di lavoro di azioni di GitHub. Identificare e scaricare la versione più recente di Linux x64 di Actions Runner, dalla [pagina versioni](https://github.com/actions/runner/releases) o eseguendo lo script rapido seguente. Questo script richiede che curl e [JQ](https://stedolan.github.io/jq/) siano presenti nella macchina virtuale.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

A questo punto dovrebbe essere disponibile un file in locale nella VM, actions-Runner-Linux-arm64- \* . tar. gz. Estrarre il tarball localmente:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Questa estrazione decomprime alcuni file in locale, inclusi uno `config.sh` `run.sh` script e, che verrà restituito a breve.

## <a name="enabling-github-actions"></a>Abilitazione di azioni di GitHub

> [!NOTE]
> Le azioni di GitHub sono [attualmente disponibili come versione beta limitata su GitHub Enterprise Server versione 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Ci siamo quasi. Configurare e abilitare le azioni di GitHub nell'istanza di GitHub Enterprise Server. È necessario [accedere alla shell amministrativa dell'istanza di GitHub Enterprise Server tramite SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh), quindi eseguire i comandi seguenti:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Prossima esecuzione:

`ghe-actions-check -s blob`

Verrà visualizzato l'output: "archiviazione BLOB è integro".

Ora che sono state configurate le azioni di GitHub, abilitarla per gli utenti. Accedere all'istanza di GitHub Enterprise Server come amministratore e selezionare l' ![ icona Rocket.](media/github-enterprise-server/rocket-icon.png) nell'angolo superiore destro di qualsiasi pagina. Nella barra laterale sinistra selezionare **Enterprise Overview**, quindi **policys**, **Actions**e selezionare l'opzione per **abilitare le azioni per tutte le organizzazioni**.

Configurare quindi il Runner dalla scheda **Runner indipendenti** . Selezionare **Aggiungi nuovo** e quindi **nuovo Runner** dall'elenco a discesa.

Nella pagina successiva verrà visualizzato un set di comandi da eseguire. è sufficiente copiare il comando per **configurare** il Runner, ad esempio:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Copiare il `config.sh` comando e incollarlo in una sessione in Action Runner (creato in precedenza).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Usare il comando run.sh per *eseguire* il Runner:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Per rendere questo Runner disponibile per le organizzazioni dell'azienda, modificare l'accesso dell'organizzazione:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

In questo articolo verrà reso disponibile a tutte le organizzazioni, ma è anche possibile limitare l'accesso a un subset di organizzazioni e anche a repository specifici.

## <a name="optional-configuring-github-connect"></a>Opzionale Configurazione di GitHub Connect

Sebbene questo passaggio sia facoltativo, è consigliabile se si prevede di utilizzare le azioni open source disponibili in GitHub.com. In questo modo è possibile creare il lavoro di altri facendo riferimento a queste azioni riutilizzabili nei flussi di lavoro.

Per abilitare la connessione a GitHub, seguire la procedura descritta in [Abilitazione dell'accesso automatico alle azioni github.com tramite GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect).

Una volta abilitata la connessione a GitHub, selezionare il **Server per l'uso di azioni da GitHub.com nell'opzione esecuzioni del flusso di lavoro** .

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Configurazione ed esecuzione del primo flusso di lavoro

Ora che sono state configurate le azioni e la connessione a GitHub, è consigliabile usare tutto questo lavoro. Ecco un flusso di lavoro di esempio che fa riferimento all'eccellente [octokit/request-Action](https://github.com/octokit/request-action), che ci permette di "creare script" in GitHub tramite interazioni usando l'API GitHub, basata su azioni di GitHub.

In questo flusso di lavoro di base verrà usato `octokit/request-action` per aprire solo un problema in GitHub usando l'API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

>[!NOTE]
>GitHub.com ospita l'azione, ma quando viene eseguita su GitHub Enterprise Server, USA *automaticamente* l'API di GitHub Enterprise Server.

Se si sceglie di non abilitare GitHub Connect, è possibile usare il seguente flusso di lavoro alternativo.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Passare a un repository nell'istanza e aggiungere il flusso di lavoro precedente come indicato di seguito: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Nella scheda **azioni** del repository, attendere l'esecuzione del flusso di lavoro.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

È anche possibile osservarne l'elaborazione da parte del Runner.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Se tutti gli elementi sono stati eseguiti correttamente, verrà visualizzato un nuovo problema nel repository, denominato "Hello World".

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Scegliere di eseguire GitHub in locale o nel cloud.":::

Congratulazioni! È stato appena completato il primo flusso di lavoro azioni su GitHub Enterprise Server, in esecuzione nel cloud privato della soluzione VMware di Azure.

Stiamo solo grattando la superficie di ciò che è possibile fare con le azioni di GitHub. Per ulteriori informazioni, vedere l'elenco delle azioni sul [Marketplace di GitHub](https://github.com/marketplace)o [crearne di personalizzate](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo viene configurata una nuova istanza di GitHub Enterprise Server, l'equivalente self-hosted di GitHub.com, nella parte superiore del cloud privato della soluzione VMware di Azure. Questa istanza include il supporto per le azioni di GitHub e usa l'archivio BLOB di Azure per la persistenza di log e artefatti. Si tratta di un'ottima combinazione per un'esperienza di sviluppo software moderna, collaborativa e sicura. Si basa su una solida base della soluzione VMware di Azure, che consente di sfruttare le risorse cloud in un'impostazione familiare.

Per ulteriori informazioni, vedere le risorse seguenti:

- [Introduzione alle azioni di GitHub](https://docs.github.com/en/actions)
- [Partecipa al programma beta](https://resources.github.com/beta-signup/)
- [Altre informazioni sull'amministrazione di GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started)