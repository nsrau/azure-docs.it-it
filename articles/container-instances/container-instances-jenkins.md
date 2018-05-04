---
title: Usare istanze di contenitore di Azure come agente di compilazione Jenkins
description: Informazioni su come usare istanze di contenitore di Azure come agente di compilazione Jenkins.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Usare istanze di contenitore di Azure come agente di compilazione Jenkins

Le istanze di contenitore di Azure offrono un ambiente isolato on demand utilizzabile in modalità burst per l'esecuzione di carichi di lavoro in contenitori. Grazie a questi attributi, le istanze di contenitore di Azure sono un'ottima piattaforma per l'esecuzione di processi di compilazione di Jenkins su vasta scala. Questo documento descrive in modo dettagliato come distribuire e usare un server Jenkins preconfigurato con istanze di contenitore di Azure come destinazione di compilazione.

Per altre informazioni sulle istanze di contenitore di Azure, vedere [Informazioni sulle istanze di contenitore di Azure][about-aci].

## <a name="deploy-jenkins-server"></a>Distribuire un server Jenkins

Nel portale di Azure fare clic su **Crea risorsa** e cercare **Jenkins**. Selezionare l'offerta Jenkins con un server di pubblicazione **Microsoft** e selezionare **Crea**.

Immettere le informazioni seguenti nel modulo di base e fare clic su **OK** al termine.

- **Nome**: nome per la distribuzione Jenkins.
- **Nome utente**: questo nome utente viene usato come utente amministratore per la macchina virtuale Jenkins.
- **Tipo di autenticazione**: è consigliabile usare Chiave pubblica SSH. Se questa opzione è selezionata, copiare una chiave pubblica SSH da usare durante l'accesso alla macchina virtuale Jenkins.
- **Sottoscrizione**: selezionare una sottoscrizione di Azure.
- **Gruppo di risorse**: creare un nuovo gruppo di risorse o selezionarne uno esistente.
- **Località**: selezionare una località per il server Jenkins.

![Impostazioni di distribuzione di base per Jenkins dal portale](./media/container-instances-jenkins/jenkins-portal-01.png)

Nel modulo delle impostazioni aggiuntive, completare le impostazioni seguenti:

- **Dimensioni**: selezionare l'opzione appropriata per le dimensioni per la macchina virtuale Jenkins.
- **Tipo di disco della macchina virtuale**: specificare Unità disco rigido o SSD (Solid-State Drive) per il server Jenkins.
- **Rete virtuale**: selezionare Rete virtuale per modificare le impostazioni predefinite (facoltativo).
- **Subnet**: selezionare Subnet, verificare le informazioni e quindi selezionare **OK**.
- **Indirizzo IP pubblico**: la selezione dell'indirizzo IP pubblico permette di assegnare un nome personalizzato e di configurare lo SKU e il metodo di assegnazione.
- **Etichetta del nome di dominio**: specificare un valore per creare un URL completo della macchina virtuale Jenkins.
- **Jenkins release type** (Tipo di versione Jenkins): selezionare il tipo di versione tra le opzioni LTS, Weekly build (Build settimanale) o Azure Verified (Verificata da Azure).

![Impostazioni di distribuzione aggiuntive per Jenkins dal portale](./media/container-instances-jenkins/jenkins-portal-02.png)

Per l'integrazione dell'entità servizio, selezionare **Auto(MSI)** per specificare la creazione automatica di un'identità di autenticazione per l'istanza di Jenkins tramite l'[identità del servizio gestita di Azure] [managed-service-identity]. Selezionare l'opzione Manuale per fornire le credenziali della propria entità servizio.

Gli agenti cloud configurano una piattaforma basata sul cloud per i processi di compilazione di Jenkins. Ai fini di questo documento, selezionare un'istanza di contenitore di Azure. Con l'agente cloud delle istanze di contenitore di Azure, ogni processo di compilazione di Jenkins viene eseguito in un'istanza di contenitore di Azure.

![Impostazioni di integrazione cloud per la distribuzione di Jenkins dal portale](./media/container-instances-jenkins/jenkins-portal-03.png)

Dopo aver specificato le impostazioni di integrazione, fare clic su **OK** e quindi di nuovo su **OK** nel riepilogo della convalida. Fare clic su **Crea** nel riepilogo delle condizioni per l'utilizzo. La distribuzione del server Jenkins richiede pochi minuti.

## <a name="configure-jenkins"></a>Configurare Jenkins

Nel portale di Azure passare al gruppo di risorse Jenkins, selezionare la macchina virtuale Jenkins e prendere nota del nome DNS.

![Istruzioni di accesso a Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

Passare al nome DNS della macchina virtuale Jenkins e copiare la stringa SSH restituita.

![Istruzioni di accesso a Jenkins](./media/container-instances-jenkins/jenkins-portal-04.png)

Aprire una sessione del terminale nel sistema di sviluppo e incollare la stringa SSH dall'ultimo passaggio. Aggiornare il nome utente in base a quello specificato durante la distribuzione del server Jenkins.

Quando viene stabilita la connessione, eseguire il comando seguente per recuperare la password amministratore iniziale.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Lasciare in esecuzione la sessione SSH e il tunnel e passare a http://localhost:8080 in un browser. Incollare la password amministratore iniziale nel campo come mostrato nell'immagine seguente. Selezionare **Continue** (Continua) al termine.

![Sbloccare Jenkins](./media/container-instances-jenkins/jenkins-portal-05.png)

Selezionare **Install suggested plugins** (Installa plug-in consigliati) per installare tutti i plug-in Jenkins consigliati.

![Installare i plug-in Jenkins](./media/container-instances-jenkins/jenkins-portal-06.png)

Creare un nuovo account utente amministratore. Questo account viene usato per l'accesso e l'uso dell'istanza di Jenkins.

![Creare l'utente di Jenkins](./media/container-instances-jenkins/jenkins-portal-07.png)

Selezionare **Save and Finish** (Salva e completa) al termine e quindi **Start using Jenkins** (Inizia a usare Jenkins) per completare la configurazione.

Jenkins è ora configurato e pronto per compilare e distribuire codice. Per questo esempio, viene usata una semplice applicazione Java per mostrare una compilazione di Jenkins su istanze di contenitore di Azure.

## <a name="create-build-job"></a>Creare il processo di compilazione

Quando si usa un'immagine del contenitore come destinazione di compilazione di Jenkins, è necessario specificare un'immagine che includa tutti gli strumenti necessari per una compilazione corretta. Per specificare l'immagine, selezionare **Manage Jenkins** (Gestisci Jenkins) > **Configure System** (Configura sistema) e scorrere verso il basso nella sezione **Cloud**. Per questo esempio, aggiornare il valore dell'immagine Docker a `microsoft/java-on-azure-jenkins-slave`.

Al termine, fare clic su **Save** (Salva) per tornare al dashboard di Jenkins.

![Configurazione cloud di Jenkins](./media/container-instances-jenkins/jenkins-aci-image.png)

Creare ora un processo di compilazione di Jenkins. Selezionare **New item** (Nuovo elemento), assegnare un nome al progetto di compilazione, ad esempio `aci-java-demo`, selezionare **Freestyle project** (Progetto Freestyle) e fare clic su **OK**.

![Creare un processo di Jenkins](./media/container-instances-jenkins/jenkins-new-job.png)

In **General** (Generale) verificare che sia selezionata l'opzione **Restrict where this project can be run** (Limita i casi in cui eseguire il progetto). Immettere `linux` per Label Expression (Espressione etichetta). Questa configurazione assicura che il processo di compilazione venga eseguito nel cloud delle istanze di contenitore di Azure.

![Creare un processo di Jenkins](./media/container-instances-jenkins/jenkins-job-01.png)

In source code management (Gestione codice sorgente) selezionare `Git` e immettere `https://github.com/spring-projects/spring-petclinic.git` per l'URL del repository. Questo repository GitHub contiene il codice dell'applicazione di esempio.

![Aggiungere codice sorgente al processo di Jenkins](./media/container-instances-jenkins/jenkins-job-02.png)

In Build (Compilazione) selezionare **add a build step** (aggiungi passaggio di compilazione) e quindi selezionare `Invoke top-level Maven targets`. Immettere `package` come obiettivo del passaggio di compilazione.

![Aggiungere un passaggio di compilazione di Jenkins](./media/container-instances-jenkins/jenkins-job-03.png)

Al termine, selezionare **Salva** .

## <a name="run-the-build-job"></a>Eseguire il processo di compilazione

Per testare il processo di compilazione e osservare le istanze di contenitore di Azure come piattaforma di compilazione, avviare manualmente una compilazione.

Selezionare **Build Now** (Compila) per avviare un processo di compilazione. L'avvio del processo richiede alcuni minuti e durante l'esecuzione lo stato dovrebbe essere indicato come mostrato nelle immagini seguenti.

![Aggiungere un passaggio di compilazione di Jenkins](./media/container-instances-jenkins/jenkins-job-status.png)

Durante l'esecuzione del processo, aprire il portale di Azure ed esaminare il gruppo di risorse Jenkins. Si noterà che è stata creata un'istanza di contenitore di Azure. L'istanza si trova all'interno di questa istanza eseguita dal processo di Jenkins.

![Compilazioni di Jenkins in istanze di contenitore di Azure](./media/container-instances-jenkins/jenkins-aci.png)

Poiché Jenkins esegue più processi rispetto al numero configurato di executor Jenkins (2 per impostazione predefinita), vengono create più istanze di contenitore di Azure.

![Compilazioni di Jenkins in istanze di contenitore di Azure](./media/container-instances-jenkins/jenkins-aci-multi.png)

Dopo aver completati tutti i processi di compilazione, le istanze di contenitore di Azure vengono rimosse.

![Compilazioni di Jenkins in istanze di contenitore di Azure](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Jenkins in Azure, vedere [Azure e Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md