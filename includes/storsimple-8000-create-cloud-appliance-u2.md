#### <a name="to-create-a-cloud-appliance"></a>Per creare un'appliance cloud

1. Nel portale di Azure passare al servizio **Gestione dispositivi StorSimple**.
2. Passare al pannello **Dispositivi**. Sulla barra dei comandi nel pannello di riepilogo dei servizi fare clic su **Creare l'appliance cloud**.
    ![Creazione dell'appliance cloud di StorSimple](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. Nel pannello **Creare l'appliance cloud** specificare i dettagli seguenti.
   
    ![Creazione dell'appliance cloud di StorSimple](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Nome**: nome univoco per l'appliance cloud.
   2. **Modello**: scegliere il modello dell'appliance cloud. Un dispositivo 8010 offre 30 TB di spazio di archiviazione Standard mentre un dispositivo 8020 fornisce 64 TB di spazio di archiviazione Premium. Specificare 8010 per distribuire gli scenari di recupero a livello di elemento dai backup. Selezionare 8020 per distribuire carichi di lavoro ad alte prestazioni e a bassa latenza o per l'uso come un dispositivo secondario per il ripristino di emergenza.
   3. **Versione**: scegliere la versione dell'appliance cloud. La versione corrisponde alla versione dell'immagine del disco virtuale usata per creare l'appliance cloud. La versione specificata per l'appliance cloud determina il dispositivo fisico da cui viene eseguito il failover o la clonazione. È importante quindi creare una versione appropriata dell'appliance cloud.
   4. **Rete virtuale**: specificare una rete virtuale da usare con questa appliance cloud. Se si usa l'archiviazione Premium, è necessario selezionare una rete virtuale supportata con l'account di archiviazione Premium. Le reti virtuali non supportate sono disattivate nell'elenco a discesa. Viene visualizzato un avviso se si seleziona una rete virtuale non supportata.
   5. **Subnet**: in base alla rete virtuale selezionata, l'elenco a discesa mostra le subnet associate. Assegnare una subnet all'appliance cloud.
   6. **Account di archiviazione**: selezionare un account di archiviazione per la conservazione dell'immagine dell'appliance cloud durante il provisioning. Questo account di archiviazione deve trovarsi nella stessa area dell'appliance cloud e della rete virtuale. Non deve essere inoltre usato per l'archiviazione dei dati da parte del dispositivo fisico o dell'appliance cloud. Per impostazione predefinita, viene creato un nuovo account di archiviazione per questo scopo specifico. Tuttavia, se già si dispone di un account di archiviazione adatto a eseguire l'operazione, selezionarlo dall'elenco. Se si crea un'appliance cloud Premium, l'elenco a discesa mostra solo gli account di archiviazione Premium.
      
      > [!NOTE]
      > L'appliance cloud può essere usata solo con gli account di archiviazione di Azure.
    
   7. Selezionare la casella di controllo per indicare di avere compreso che i dati archiviati nell'appliance cloud vengono ospitati in un data center Microsoft.
       * Quando si utilizza soltanto un dispositivo fisico, la chiave di crittografia viene conservata nel dispositivo; di conseguenza, Microsoft non può decrittografarli.

       * Quando si usa un'appliance cloud, la chiave di crittografia e la chiave di decrittografia vengono archiviate in Microsoft Azure. Per altre informazioni, vedere le [considerazioni relative alla sicurezza per l'uso di un'appliance cloud](../articles/storsimple/storsimple-security.md).
   8. Fare clic su **Crea** per effettuare il provisioning di un'appliance cloud. Il provisioning del dispositivo potrebbe durare circa 30 minuti. Al termine della creazione dell'appliance cloud, viene inviata una notifica all'utente. Passare al pannello Dispositivi. L'elenco di dispositivi viene aggiornato per visualizzare l'appliance cloud. Lo stato dell'appliance è **Pronto per la configurazione**.
      
      ![L'appliance cloud StorSimple è pronta per la configurazione](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)

