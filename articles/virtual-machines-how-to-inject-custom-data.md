<properties 
	pageTitle="Inserimento di dati personalizzati in macchine virtuali di Azure" 
	description="In questo argomento viene descritto come inserire dati personalizzati in una macchina virtuale di Azure durante la creazione dell'istanza e come individuare i dati personalizzati in Windows o Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/1/2014" 
	ms.author="rasquill"/>



# Inserimento di dati personalizzati in una macchina virtuale di Azure 

L'inserimento di uno script o di altri dati in una macchina virtuale di Azure durante il provisioning è uno scenario molto comune e indipendente dal sistema operativo usato per la distribuzione, Microsoft Windows o Linux. In questo argomento viene spiegato come:

- Inserire i dati in una macchina virtuale di Azure durante il provisioning

- Recuperare i dati sia in Windows che in Linux e 

- Usare strumenti speciali disponibili in alcuni sistemi per rilevare e gestire automaticamente i dati personalizzati.

> [AZURE.NOTE] Questo argomento viene trattato anche in [questo post del blog di Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/), che viene mantenuto aggiornato man mano che vengono aggiunte nuove funzionalità.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
Contenuto dell'argomento:

+ [Inserimento di dati personalizzati nella macchina virtuale di Azure](#injectingCustomData)

+ [Uso di dati personalizzati nella macchina virtuale](#usingCustomData)

+ [Passaggi successivi](#nextsteps)


## <a id="injectingCustomData"></a>Inserimento di dati personalizzati nella macchina virtuale di Azure

Questa funzionalità è attualmente supportata solo nell'[interfaccia della riga di comando multipiattaforma di Microsoft Azure](https://github.com/Azure/azure-sdk-tools-xplat). È possibile usare una delle numerose opzioni per il comando `azure vm create`, ma si è scelto di descrivere un approccio molto semplice nell'esempio seguente. 

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## <a id="usingCustomData"></a>Uso di dati personalizzati nella macchina virtuale
 
+ Se la macchina virtuale di Azure è una macchina virtuale Windows, il file di dati personalizzato viene salvato in `%SYSTEMDRIVE%\AzureData\CustomData.bin` e, anche se era codificato in Base 64 per il trasferimento dal computer locale alla nuova macchina virtuale, viene automaticamente decodificato e può essere subito aperto o usato. 

   > [AZURE.NOTE] Se il file esiste viene sovrascritto. La sicurezza nella directory viene impostata su **System:Full Control** e **Administrators:Full Control**.

+ Se la macchina virtuale di Azure è una macchina virtuale Linux, il file di dati personalizzato viene salvato in questi due percorsi, ma i dati vengono codificati in Base 64, pertanto sarà necessario prima decodificarli.

    + In `/var/lib/waagent/ovf-env.xml`
    + In `/var/lib/waagent/CustomData` 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a id="nextsteps"></a>Passaggi successivi: Uso di cloud-init

Se la macchina virtuale di Azure si trova in un'immagine Ubuntu, è possibile usare cloud-init per avviare uno script e usare automaticamente i dati personalizzati (o, se il file di dati personalizzato è uno script, eseguirlo). Per altre informazioni, vedere la [documentazione su cloud-init per Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Aggiungere il riferimento all'API REST di gestione del servizio ruolo](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interfaccia della riga di comando multipiattaforma di Microsoft Azure](https://github.com/Azure/azure-sdk-tools-xplat)


<!--HONumber=47-->
