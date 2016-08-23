<properties
    pageTitle="Creare una macchina virtuale Linux tramite il portale di Azure | Microsoft Azure"
    description="Creare una macchina virtuale Linux tramite il portale di Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# Creare una VM Linux in Azure usando il portale.

> [AZURE.NOTE] Gli utenti sono invitati a fornire il proprio contributo per il miglioramento della documentazione sulle macchine virtuali Linux di Azure eseguendo questo [rapido sondaggio ](https://aka.ms/linuxdocsurvey) sulle proprie esperienze. Ogni risposta è utile per aiutare gli utenti a svolgere al meglio la propria attività.

Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com/) per creare rapidamente una macchina virtuale Linux. Gli unici requisiti sono [un account Azure](https://azure.microsoft.com/pricing/free-trial/) e [file di chiavi SSH pubbliche e private](virtual-machines-linux-mac-create-ssh-keys.md).

> [AZURE.NOTE] Se si sceglie di usare una password per proteggere l'accesso alla VM, la password deve contenere almeno 12 caratteri, di cui almeno un carattere maiuscolo, almeno un carattere minuscolo, almeno un carattere speciale e almeno un numero.


1. Dopo avere eseguito l'accesso al portale di Azure con l'identità dell'account Azure, fare clic su **+ Nuovo** nell'angolo superiore sinistro:

    ![schermata1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Fare clic su **Macchine virtuali** nel **Marketplace**, quindi su **Ubuntu Server 14.04 LTS** dall'elenco di immagini **App in primo piano**. Verificare nella parte inferiore che il modello di distribuzione sia `Resource Manager` e quindi fare clic su **Crea**.

    ![schermata2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Nella pagina **Informazioni di base** immettere:
    - Nome della macchina virtuale
    - Nome utente dell'utente amministratore
    - Tipo di autenticazione impostato su **Chiave pubblica SSH**
    - Chiave pubblica SSH sotto forma di stringa, per impostazione predefinita dalla directory `~/.ssh/`
    - Nome di un gruppo di risorse, per creare un nuovo gruppo di distribuzione, o selezionare un gruppo esistente

    Fare clic su **OK** per continuare e scegliere le dimensioni della macchina virtuale, che saranno simili alle seguenti:

    ![schermata3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. Scegliere come dimensioni **DS1** per installare Ubuntu in un'unità SSD Premium e fare clic su **Seleziona** per configurare le impostazioni.

    ![schermata4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. In **Impostazioni** lasciare le impostazioni predefinite per i valori di archiviazione e di rete e fare clic su **OK** per visualizzare il riepilogo. Si noti che il tipo di disco è stato impostato su Premium (SSD) scegliendo DS1. La lettera **S** sta per SSD.

    ![schermata5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. Confermare le impostazioni per la nuova macchina virtuale Ubuntu e fare clic su **OK**.

    ![schermata6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. Aprire il dashboard del portale e in **Interfacce di rete** scegliere la scheda di rete.

    ![schermata7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. Aprire il menu di indirizzi IP pubblici sotto le impostazioni della scheda di rete.

    ![schermata8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. Usare SSH per accedere all'indirizzo IP pubblico con la chiave SSH pubblica.

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## Passaggi successivi

A questo punto è stata creata rapidamente una VM Linux da usare per scopi di test o dimostrazione. Per creare una VM Linux personalizzata per l'infrastruttura, è possibile vedere gli articoli seguenti.

- [Creare una VM Linux in Azure usando i modelli](virtual-machines-linux-cli-deploy-templates.md)
- [Creare una VM Linux protetta con SSH in Azure usando i modelli](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Creare una VM Linux usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md)

Questi articoli illustrano come iniziare a compilare un'infrastruttura di Azure oltre a un numero qualsiasi di strumenti di orchestrazione, configurazione e distribuzione delle infrastrutture proprietarie e open source.

<!---HONumber=AcomDC_0817_2016-->