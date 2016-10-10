<properties
   pageTitle="Red Hat Update Infrastructure (RHUI) | Microsoft Azure"
   description="Informazioni sul servizio Red Hat Update Infrastructure (RHUI) per istanze di Red Hat Enterprise Linux in Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="borisb"/>

# Red Hat Update Infrastructure (RHUI) per macchine virtuali Red Hat Enterprise Linux su richiesta in Azure

Le macchine virtuali create dalle immagini di Red Hat Enterprise Linux (RHEL) su richiesta disponibili in Azure Marketplace vengono registrate per accedere al servizio Red Hat Update Infrastructure (RHUI) distribuito in Azure. Le istanze di RHEL su richiesta potranno accedere a repository yum a livello di area e potranno ricevere aggiornamenti incrementali.

L'elenco di repository yum, gestito da RHUI, viene configurato nell'istanza di RHEL durante il provisioning. Non è necessario eseguire altre attività di configurazione. Eseguire `yum update` non appena l'istanza di RHEL è pronta per l'installazione degli aggiornamenti più recenti.

> [AZURE.NOTE] L'infrastruttura RHUI di Azure è stata aggiornata di recente (settembre 2016) e richiede modifiche alla configurazione delle istanze di RHEL esistenti per l'accesso ininterrotto a tale infrastruttura. Per informazioni dettagliate, fare riferimento alla sezione Aggiornamento dell'infrastruttura RHUI di Azure.


## Aggiornamento dell'infrastruttura RHUI di Azure
A partire da settembre 2016, Azure offre un nuovo set di server RHUI (Red Hat Update Infrastructure). Questi server vengono distribuiti con [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/) in modo che un singolo endpoint (rhui-1.micrsoft.com) possa essere usato da qualsiasi macchina virtuale indipendentemente dall'area. Usano inoltre un certificato SSL concatenato a un'autorità di certificazione nota (radice Baltimore). Poiché l'esecuzione automatica di questo aggiornamento può essere pericolosa per alcuni clienti che dispongono di ACL o tabelle di routing personalizzate per i server di aggiornamento RHUI, è necessario acconsentire esplicitamente all'aggiornamento. Questa pagina illustra la procedura manuale per l'introduzione di questi nuovi server e uno script completo per l'esecuzione automatica della procedura (dopo la verifica dei singoli passaggi). Le nuove immagini RHEL con pagamento in base al consumo in Azure Marketplace (versioni di settembre 2016 o successive) punteranno automaticamente ai nuovi server RHUI di Azure senza richiedere altre operazioni.

### Sequenza temporale dell'introduzione della nuova infrastruttura RHUI di Azure

| Data | Nota |
| --- | --- |
|22 settembre 2016 | I server RHUI e le istruzioni di installazione sono disponibili per l'uso. Le macchine virtuali distribuite mediante le nuove immagini RHEL con pagamento in base al consumo (versioni di settembre 2016) disponibili in Marketplace useranno automaticamente i nuovi server RHUI, ma le macchine virtuali esistenti richiedono un consenso esplicito.
|1 novembre 2016 | Le immagini legacy delle macchine virtuali RHEL con pagamento in base al consumo (che usano i server RHUI di Azure precedenti) verranno rimosse dalla raccolta di Azure Marketplace.
|16 gennaio 2017 | I server RHUI di Azure precedenti verranno ritirati. Per mantenere l'accesso all'infrastruttura RHUI di Azure, aggiornare entro questa data tutte le macchine virtuali RHEL con pagamento in base al consumo interessate.

### Indirizzi IP per i nuovi server RHUI

```
13.91.45.82
52.187.72.244
52.174.166.207
40.112.59.164
```

### Procedura di aggiornamento manuale per l'uso dei nuovi server RHUI di Azure

Scaricare (tramite curl) la firma con chiave pubblica

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Verificare la chiave scaricata

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Controllare l'output, verificare `keyid` e `user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Installare la chiave pubblica

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Scaricare, verificare e installare l'RPM del client

Scaricare: per RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Per RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Verificare:

```
rpm -Kv azureclient.rpm
```

Controllare nell'output che la firma del pacchetto sia corretta

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Installare l'RPM

```
sudo rpm -U azureclient.rpm
```

Al termine, verificare che sia possibile accedere all'infrastruttura RHUI di Azure dalla macchina virtuale

### Script all-in-one per automatizzare l'attività precedente
Usare lo script seguente, in base alle proprie esigenze, per automatizzare l'attività di aggiornamento delle macchine virtuali interessate ai nuovi server RHUI di Azure.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## Panoramica di RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) offre una soluzione altamente scalabile per gestire il contenuto del repository yum per le istanze cloud di Red Hat Enterprise Linux ospitate dai provider cloud certificati da Red Hat. In base al progetto Pulp upstream, RHUI consente ai provider cloud di eseguire il mirroring locale del contenuto del repository ospitato da Red Hat e creare repository personalizzati con il proprio contenuto e rendere disponibili tali repository per un ampio gruppo di utenti finali tramite un sistema di distribuzione del contenuto con bilanciamento del carico.

## Aree in cui viene è disponibile l'infrastruttura RHUI
Il servizio RHUI è disponibile in tutte le aree in cui sono disponibili le immagini di RHEL su richiesta. Include attualmente tutte le aree pubbliche elencate nella pagina [Stato di Azure](https://azure.microsoft.com/status/). L'accesso al servizio RHUI per macchine virtuali sottoposte a provisioning da immagini di RHEL su richiesta è incluso nel rispettivo prezzo. La disponibilità locale/nazionale del cloud verrà aggiornata in base all'espansione della disponibilità di RHEL su richiesta in futuro.

> [AZURE.NOTE] L'accesso all'istanza di RHUI ospitata in Azure è limitato alle macchine virtuali comprese negli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## Ottenere aggiornamenti da un altro repository di aggiornamenti

Se è necessario ottenere aggiornamenti da un repository di aggiornamenti diverso, invece del servizio RHUI ospitato in Azure, sarà necessario annullare la registrazione delle istanze da RHUI e ripeterne la registrazione nell'infrastruttura di aggiornamento desiderata, ad esempio Red Hat Satellite o Red Hat Customer Portal CDN. Saranno necessarie sottoscrizioni di Red Hat appropriate per questi servizi e una registrazione per il programma [Red Hat Cloud Access in Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Per annullare la registrazione a RHUI e registrarsi all'infrastruttura di aggiornamento desiderata, seguire questa procedura.

1.	Modificare /etc/yum.repos.d/rh-cloud.repo e sostituire tutte le occorrenze di `enabled=1` con `enabled=0`. ad esempio:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Modificare /etc/yum/pluginconf.d/rhnplugin.conf e sostituire tutte le occorrenze di `enabled=0` con `enabled=1`.
3.	Eseguire quindi la registrazione all'infrastruttura desiderata, ad esempio il portale dei clienti di Red Hat. Per informazioni su come [eseguire la registrazione e la sottoscrizione di un sistema nel portale dei clienti di Red Hat](https://access.redhat.com/solutions/253273), vedere la guida della soluzione Red Hat.

> [AZURE.NOTE] L'accesso al servizio RHUI ospitato in Azure è incluso nel prezzo dell'immagine di RHEL con pagamento in base al consumo. L'annullamento della registrazione di una macchina virtuale RHEL con pagamento in base al consumo dal servizio RHUI ospitato in Azure non converte la macchina virtuale in una VM di tipo Bring Your Own License (BYOL). È quindi possibile che vengano applicati addebiti doppi se si registra la stessa macchina virtuale in un'altra origine di aggiornamenti.
> 
> Se è necessario usare in modo continuativo un'infrastruttura di aggiornamento diversa dall'infrastruttura RHUI ospitata in Azure, prendere in considerazione la creazione e la distribuzione di immagini personalizzate di tipo BYOL, come descritto nell'articolo [Preparare una macchina virtuale basata su RedHat per Azure](virtual-machines-linux-redhat-create-upload-vhd.md).

## Passaggi successivi
Per creare una macchina virtuale Red Hat Enterprise Linux dall'immagine con pagamento in base al consumo di Azure Marketplace e sfruttare i vantaggi dell'infrastruttura RHUI ospitata in Azure, passare ad [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Sarà possibile usare `yum update` nell'istanza di RHEL senza impostazioni aggiuntive.

<!---HONumber=AcomDC_0928_2016-->