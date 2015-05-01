
<properties 
    pageTitle="Requisiti delle immagini RemoteApp"
    description="Informazioni sui requisiti per la creazione di immagini da usare con RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2015" 
    ms.author="elizapo" />



# Requisiti per le immagini di RemoteApp
RemoteApp usa un'immagine di Windows Server 2012 R2 per ospitare tutti i programmi da condividere con gli utenti. Per creare un'immagine di RemoteApp personalizzata, è possibile iniziare con un'immagine esistente o [crearne una nuova](remoteapp-create-custom-image.md). I requisiti per le immagini che possono essere caricate e usate con Azure RemoteApp sono i seguenti:


- Le applicazioni personalizzate non archiviano dati in locale nell'immagine. Queste immagini sono senza stato e devono contenere solo le applicazioni.
- L'immagine non contiene dati che possono essere persi.
- La dimensione dell'immagine dev'essere un multiplo di MB. Se si prova a caricare un'immagine che non è un multiplo esatto, l'operazione non andrà a buon fine.
- L'immagine deve avere una dimensione massima di 127 GB. 
- Deve essere inclusa in un file VHD (i file VHDX non sono attualmente supportati).
- Il VHD non deve essere una macchina virtuale di seconda generazione.
- Il file VHD può essere di dimensioni fisse o a espansione dinamica. È consigliabile un file VHD a espansione dinamica perché i tempi di caricamento di questo file in Azure sono inferiori rispetto a uno di dimensioni fisse.
- Il disco deve essere inizializzato con lo stile di partizione MBR (Master Boot Record). Lo stile di partizione basato sulla tabella di partizione GUID (GPT) non è supportato. 
- Il file VHD deve contenere una singola installazione di Windows Server 2012 R2. Può contenere più volumi, ma solo uno che contiene un'installazione di Windows. 
- È necessario installare il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop.
- Il ruolo Gestore connessione Desktop remoto  *non* deve essere installato.
- EFS (Encrypting File System) deve essere disabilitato.
- L'immagine deve essere preparata con SYSPREP usando i parametri **/oobe /generalize /shutdown** (NON usare il parametro **/mode:vm**).
- Il caricamento del disco VHD da una catena di snapshot non è supportato.


<!--HONumber=52-->