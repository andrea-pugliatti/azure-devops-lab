# Consegna UD03 — Domande sui concetti

Per ciascuna domanda 1–8 riporta una risposta motivata.

1. L'autenticazione si limita a verificare l'identità del soggetto, mentre l'autorizzazione valuta se quell'identità ha effettivamente il permesso di compiere una specifica azione.
2. I ruoli Microsoft Entra operano sulle identità entro i limiti del ruolo. I ruoli Azure (Azure RBAC) operano direttamente sulle risorse cloud Azure.
3. Una role assignment è formata da principal, l'identità a cui viene concesso il permesso, role definition, che elenca le azioni consentite o escluse, e lo scope, cioé l'ambito in cui si applica l'assegnazione.
4. Assegnare Contributor alla sottoscrizione esporrebbe inutilmente l'intero ambiente al rischio di errori operativi o a un impatto molto più grave in caso di compromissione delle credenziali.
Questo rispetta il principio del minimo privilegio (modello Zero Trust), cioé concedere solo i permessi strettamente necessari e limitare la superficie al solo resource group anziché all'intera sottoscrizione.
5. Perché l'ereditarietà in Azure RBAC fluisce dall'alto verso il basso lungo la gerarchia degli scope (management group -> sottoscrizione -> resource group -> risorsa). L'assegnazione di ruolo non risiede fisicamente sulla risorsa figlia, ma è stata definita a un livello superiore e per modificarla o revocarla è necessario intervenire direttamente al livello in cui è stata creata.
6. No. I tag servono unicamente come metadati per organizzare le risorse e tracciare i costi. Essi non hanno alcuna funzione di autorizzazione né possono impedire operazioni.
7. Il ruolo Reader fa un controllo basato sull'identità. A un utente con ruolo Reader viene negata a monte l'autorizzazione di eliminare o modificare le risorse. Il lock CanNotDelete è una protezione applicata alla risorsa. Si applica a prescindere dall'identità, impedendo la cancellazione della risorsa anche a quegli utenti che avrebbero i permessi per farlo.
8. Perché il budget funge solo da strumento di monitoraggio e soglia di avviso. Una volta raggiunto il limite invia notifiche, ma non blocca i costi né spegne automaticamente le risorse. Senza interventi manuali o automazioni collegate alla notifica, il consumo e, quindi, la spesa continuano a crescere.

