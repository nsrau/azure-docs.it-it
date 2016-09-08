<properties
   pageTitle="Abilitare o disabilitare il monitoraggio delle VM di Azure"
   description="Descrive come abilitare o disabilitare il monitoraggio delle VM di Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# Abilitare o disabilitare il monitoraggio delle VM di Azure

Questa sezione descrive come abilitare o disabilitare il monitoraggio nelle macchine virtuali in esecuzione in Azure. Per impostazione predefinita, il monitoraggio è abilitato nelle macchine virtuali di Azure se distribuite dal [portale di Azure](https://portal.azure.com) e grafici sul monitoraggio vengono forniti per impostazione predefinita ogni minuto. È possibile abilitare o disabilitare il monitoraggio usando il portale o l'interfaccia della riga di comando di Azure per Mac, Linux e Windows (l'interfaccia della riga di comando di Azure).

## Abilitare/Disabilitare il monitoraggio dal portale di Azure
 
È possibile abilitare il monitoraggio della VM di Azure, che fornisce dati sull'istanza ogni minuto (sono incluse le modifiche dell'archivio). Dati di diagnostica dettagliati sono quindi disponibili per la VM nei grafici del portale o tramite l'API. Per impostazione predefinita, nel portale di Azure il monitoraggio è abilitato, ma è possibile disattivarlo come illustrato di seguito. È possibile abilitare il monitoraggio mentre la VM si trova nello stato in corso di esecuzione o arrestato.

- Aprire il portale di Azure all'indirizzo **[https://portal.azure.com](https://portal.azure.com)**

- Nel riquadro di spostamento a sinistra fare clic su Macchine virtuali.

- Nell'elenco Macchine virtuali selezionare un'istanza in esecuzione o arrestata. Verrà aperto il pannello Macchina virtuale.

- Fare clic su "Tutte le impostazioni".

- Fare clic su "Diagnostica".

- Impostare lo stato su Sì o No. In questo pannello è anche possibile selezionare il livello di dettagli di monitoraggio che si vuole abilitare per la macchina virtuale.

[Azure.Note] L'impostazione Sì di Diagnostica è quella predefinita quando si crea una nuova macchina virtuale.

![Abilitare/Disabilitare il monitoraggio dal portale di Azure.][1]


## Abilitare/Disabilitare il monitoraggio con l'interfaccia della riga di comando di Azure
 
Per abilitare il monitoraggio per una VM di Azure.

- Creare un file denominato, ad esempio, PrivateConfig.json con il contenuto seguente. { "storageAccountName":"l'account di archiviazione per ricevere i dati", "storageAccountKey":"la chiave dell'account" }
- Eseguire il comando dell'interfaccia della riga di comando di Azure seguente:

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] È possibile passare dalla versione 2.0 a una successiva, se disponibile.

Per altri dettagli sulla configurazione della metrica di monitoraggio ed esempi, vedere il documento **[Uso dell'estensione di diagnostica Linux per monitorare le prestazioni delle VM Linux e i dati di diagnostica](virtual-machines-linux-classic-diagnostic-extension.md).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

<!---HONumber=AcomDC_0824_2016-->