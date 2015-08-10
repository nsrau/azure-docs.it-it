
<properties 
    pageTitle="Requisiti delle immagini di Azure RemoteApp"
    description="Informazioni sui requisiti per la creazione di immagini da usare con Azure RemoteApp" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2015" 
    ms.author="elizapo" />



# Requisiti per le immagini di Azure RemoteApp
Azure RemoteApp usa un'immagine di Windows Server 2012 R2 per ospitare tutti i programmi da condividere con gli utenti. Per creare un’immagine personalizzata, è possibile iniziare con un’immagine esistente oppure [crearne una nuova](remoteapp-create-custom-image.md).

> [AZURE.TIP]Non tutti sanno che la sottoscrizione di Azure RemoteApp consente di accedere a un'immagine di Windows Server 2012 R2 nella raccolta di macchine Virtuali di Azure che è possibile utilizzare per creare la propria immagine modello [Provare](remoteapp-image-on-azurevm.md).


I requisiti per l'immagine che possono essere caricati e usati con l'app Azure RemoteApp sono i seguenti:


- Le applicazioni personalizzate non archiviano dati in locale nell'immagine. Queste immagini sono senza stato e devono contenere solo le applicazioni.
- L'immagine non contiene dati che possono essere persi.
- La dimensione dell'immagine dev'essere un multiplo di MB. Se si prova a caricare un'immagine che non è un multiplo esatto, l'operazione non andrà a buon fine.
- L'immagine deve avere una dimensione massima di 127 GB. 
- Deve essere inclusa in un file VHD (i file VHDX non sono attualmente supportati).
- Il file VHD non deve essere una macchina virtuale di seconda generazione.
- Il file VHD può essere di dimensioni fisse o a espansione dinamica. È consigliabile un file VHD a espansione dinamica perché i tempi di caricamento di questo file in Azure sono inferiori rispetto a uno di dimensioni disse.
- Il disco deve essere inizializzato con lo stile di partizione MBR (Master Boot Record). Lo stile di partizione basato sulla tabella di partizione GUID (GPT) non è supportato. 
- Il file VHD deve contenere una singola installazione di Windows Server 2012 R2. Può contenere più volumi, ma solo uno che contiene un'installazione di Windows. 
- È necessario installare il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop.
- Il ruolo Gestore connessione Desktop remoto *non* deve essere installato.
- EFS (Encrypting File System) deve essere disabilitato.
- L'immagine deve essere preparata con SYSPREP usando i parametri **/oobe /generalize /shutdown** (NON usare il parametro **/mode:vm**).
- Il caricamento del disco VHD da una catena di snapshot non è supportato.
 

<!---HONumber=July15_HO5-->