<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Risorsa</th>
   <th align="left" valign="middle">Limite predefinito</th>
   <th align="left" valign="middle">Limite massimo</th>
</tr>
<tr>
   <td valign="middle"><p>Account di Servizi multimediali di Azure in una singola sottoscrizione</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>Asset per ogni account di Servizi multimediali di Azure</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>1.000.000</p></td>
</tr>
<tr>
   <td valign="middle"><p>Attività concatenate per processo</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>30</p></td>
</tr>
<tr>
   <td valign="middle"><p>Asset per attività</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Asset per processo</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Processi per ogni account di Servizi multimediali di Azure </p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50.000<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Localizzatori univoci associati a un asset contemporaneamente</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>5<sup>4</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Canali live per ogni account di Servizi multimediali di Azure </p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>N/D<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Programmi con stato arrestato per canale </p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>N/D<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Programmi con stato in esecuzione per canale </p></td>
   <td valign="middle"><p>3</p></td>
   <td valign="middle"><p>N/D<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Endpoint di streaming con stato in esecuzione per ogni account di Servizi multimediali di Azure</p></td>
   <td valign="middle"><p>2</p></td>
   <td valign="middle"><p>N/D<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Unità di streaming per endpoint di streaming </p></td>
   <td valign="middle"><p>10 </p></td>
   <td valign="middle"><p>N/D<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Unità di codifica per ogni account di Servizi multimediali di Azure </p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>N/D<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup> È possibile richiedere l'aggiornamento dei limiti per questa quota aprendo un ticket di supporto. Non creare altri account di Servizi multimediali di Azure per aumentare i limiti, ma inviare invece un ticket di supporto.

<sup>2</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. È possibile eliminare i processi obsoleti usando **IJob.Delete** o la richiesta HTTP **DELETE**.

<sup>3</sup> L'elenco di entità Job restituite può includere al massimo 1.000 voci per richiesta. Per tenere traccia di tutte le entità Job inviate, si possono usare le opzioni top/skip descritte nell'argomento [Opzioni query di sistema OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> I localizzatori non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

<!---HONumber=July15_HO3-->