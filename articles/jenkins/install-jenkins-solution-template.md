---
title: "Creare la prima unità master Jenkins in una macchina virtuale Linux (Ubuntu) in Azure"
description: Usare il modello di soluzione per distribuire Jenkins.
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 80fd9ee9b9de5c7547b9f840ac78a60d52153a5a
ms.openlocfilehash: 06d6d305eb9711768dc62a04726359e6280d1b69
ms.contentlocale: it-it
ms.lasthandoff: 08/14/2017

---

# <a name="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure"></a>Creare la prima unità master Jenkins in una macchina virtuale Linux (Ubuntu) in Azure

Questa guida introduttiva spiega come installare l'ultima versione stabile di Jenkins in una macchina virtuale Linux (Ubuntu 14.04 LTS) insieme agli strumenti e ai plug-in configurati per l'uso con Azure. Gli strumenti comprendono:
<ul>
<li>Git per il controllo del codice sorgente</li>
<li>Plug-in per le credenziali di Azure per la connessione in modo sicuro</li>
<li>Plug-in Agenti di macchine virtuali di Azure per la compilazione elastica, il test e l'integrazione continua</li>
<li>Plug-in Archiviazione di Azure per l'archiviazione degli elementi</li>
<li>Interfaccia della riga di comando di Azure per la distribuzione di app tramite script</li>
</ul>

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account Azure gratuito.
> * Creare un'unità master Jenkins in una macchina virtuale di Azure con un modello di soluzione. 
> * Eseguire la configurazione iniziale per Jenkins.
> * Installare i plug-in consigliati.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins"></a>Creare la macchina virtuale in Azure distribuendo il modello di soluzione per Jenkins

I modelli di avvio rapido di Azure consentono di distribuire in modo rapido e affidabile tecnologie complesse in Azure.  Azure Resource Manager consente di effettuare il provisioning delle applicazioni usando un [modello dichiarativo](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins). In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Si usa lo stesso modello per distribuire più volte l'applicazione durante ogni fase del ciclo di vita dell'applicazione.

Visualizzare le informazioni su [piani e prezzi](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview) per questo modello per comprendere le opzioni di costo.

Andare all'[immagine del Marketplace per Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview) e fare clic su **GET IT NOW** (Ottieni subito).  

Nel portale di Azure fare clic su **Crea**.  Questo modello richiede l'uso di Resource Manager, pertanto l'elenco a discesa dello strumento per i modelli è disabilitato.
   
![Finestra di dialogo del portale di Azure](./media/install-jenkins-solution-template/ap-create.png)

Nella scheda **Configurare le impostazioni di base**:

![Configurare le impostazioni di base](./media/install-jenkins-solution-template/ap-basic.png)

* Specificare un nome per l'istanza di Jenkins.
* Selezionare un tipo di disco della macchina virtuale.  Per i carichi di lavoro di produzione, scegliere un'unità SSD e una macchina virtuale più grande per ottenere prestazioni migliori.  Per altre informazioni sui tipi di dischi di Azure, vedere [qui](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage).
* Nome utente: deve soddisfare i requisiti di lunghezza e non deve includere parole riservate o caratteri non supportati. Non sono consentiti nomi come "admin".  Per altre informazioni, vedere [qui](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq) per i requisiti relativi a nome utente e password.
* Tipo di autenticazione: creare un'istanza protetta da password o da una [chiave pubblica SSH](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows). Se si usa una password, deve soddisfare tre dei requisiti seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale.
* Mantenere **LTS** come tipo di versione di Jenkins.
* Selezionare una sottoscrizione.
* Creare un gruppo di risorse o usarne uno esistente vuoto. 
* Selezionare una località.

Nella scheda **Configurare le opzioni aggiuntive**:

![Configurare le opzioni aggiuntive](./media/install-jenkins-solution-template/ap-addtional.png)

* Specificare un'etichetta del nome di dominio per identificare in modo univoco l'unità master Jenkins.

Fare clic su **OK** per andare al passaggio successivo. 

Al termine della convalida, fare clic su **OK** per scaricare il modello e i parametri. 

Selezionare quindi **Acquisto** per effettuare il provisioning di tutte le risorse.

## <a name="setup-ssh-port-forwarding"></a>Configurare il port forwarding SSH

Per impostazione predefinita, l'istanza di Jenkins usa il protocollo http ed è in ascolto sulla porta 8080. Gli utenti non devono autenticarsi su protocolli non protetti.
    
Configurare il port forwarding per visualizzare l'interfaccia utente di Jenkins nel computer locale.

### <a name="if-you-are-using-windows"></a>Se si usa Windows:

Installare PuTTY ed eseguire questo comando se si usa la password per la sicurezza di Jenkins:
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Per accedere, immettere la password.

![Per accedere, immettere la password.](./media/install-jenkins-solution-template/jenkins-pwd.png)

Se si usa SSH, eseguire questo comando:
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

### <a name="if-you-are-using-linux-or-mac"></a>Se si usa Linux o Mac:

Se si usa una password per la sicurezza dell'unità master Jenkins, eseguire questo comando:
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Per accedere, immettere la password.

Se si usa SSH, eseguire questo comando:
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

## <a name="connect-to-jenkins"></a>Connettersi a Jenkins
Dopo avere avviato il tunnel, passare a http://localhost:8080/ nel computer locale.

Sbloccare il dashboard di Jenkins per la prima volta con la password amministratore iniziale.

![Sbloccare Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Per ottenere un token, accedere tramite SSH alla macchina virtuale ed eseguire `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.

![Sbloccare Jenkins](./media/install-jenkins-solution-template/jenkins-ssh.png)

Verrà chiesto di installare i plug-in consigliati.

Creare quindi un utente amministratore per l'unità master di Jenkins.

L'istanza di Jenkins è ora pronta per l'uso. È possibile accedere a una visualizzazione di sola lettura passando a http://\<Nome DNS pubblico dell'istanza creata\>.

![Jenkins è pronto per l'uso.](./media/install-jenkins-solution-template/jenkins-welcome.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * È stata creata un'unità master di Jenkins con il modello di soluzione.
> * È stata eseguita la configurazione iniziale di Jenkins.
> * Sono stati installati i plug-in.

Seguire questo collegamento per sapere come usare Agenti di macchine virtuali di Azure per l'integrazione continua con Jenkins.

> [!div class="nextstepaction"]
> [Macchine virtuali di Azure come agenti di Jenkins](jenkins-azure-vm-agents.md)

