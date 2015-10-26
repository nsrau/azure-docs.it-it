<properties 
	pageTitle="Impostazioni del Registro di sistema di Cloud App Discovery per i servizi proxy" 
	description="Questo argomento illustra tutti i passaggi da eseguire per impostare la porta necessaria sui computer che eseguono l'agente Cloud App Discovery." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Impostazioni del Registro di sistema di Cloud App Discovery per i servizi proxy

Per impostazione predefinita, l'agente Cloud App Discovery è configurato solo per l'uso delle porte 80 o 443. Se si prevede di installare Cloud App Discovery in un ambiente con un server proxy che usa una porta personalizzata (non la 80 né la 443), è necessario configurare gli agenti per l'uso di questa porta. La configurazione si basa su una chiave del Registro di sistema.


Questo argomento illustra tutti i passaggi da eseguire per impostare la porta necessaria sui computer che eseguono l'agente Cloud App Discovery.



**Per modificare la porta usata dal computer che esegue l'agente Cloud App Discovery, eseguire la procedura seguente:**


1. Avviare l'Editor del Registro di sistema. <br> ![Esegui](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Passare alla chiave seguente del Registro di sistema oppure crearla: <br> **HKLM\_LOCAL\_MACHINE\\Software\\Microsoft\\Cloud App Discovery\\Endpoint**

3. Creare un nuovo valore **multistringa** denominato **Ports**. ![Nuovo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Per aprire la finestra di dialogo **Modifica multistringhe**, fare doppio clic sul valore Ports.


5. Nella casella di testo Dati valore digitare i valori seguenti e aggiungere tutte le porte personalizzate usate dall'organizzazione: <br><br> **80** <br> **8080** <br> **8118** <br> **8888** <br> **81** <br> **12080** <br> **6999** <br> **30606** <br> **31595** <br> **4080** <br> **443** <br> **1110** <br><br> ![Modifica multistringhe](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Fare clic su **OK** per chiudere la finestra di dialogo **Modifica multistringhe**.



**Risorse aggiuntive**


* [Come individuare app cloud non autorizzate usate nell'organizzazione](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=Oct15_HO3-->