# Bulletin Monitor

There have been multiple suggestions to develop a tool that regularly or at least every morning automatically scans the bulletin and sends a message based on the number of keywords and laws if there is relevant content. This would greatly assist civil organizations in timely responding to freshly published, often not yet effective regulations.

The bulletins are uploaded here (PDF): https://magyarkozlony.hu/

The most recent bulletin in PDF format is always highlighted, and the tool should monitor it.

Sometimes updates occur daily, and other times multiple bulletins are published in a day, while occasionally there may be a gap of 3-4 days.

## Relevant websites to monitor:
- Public Procurement Bulletin: https://www.kozbeszerzes.hu/ertesito/ downloadable PDF, updated almost every weekday
- Hungarian Bulletin: https://magyarkozlony.hu/ PDF
- Anonymized Court Decisions: https://eakta.birosag.hu/anonimizalt-hatarozatok DOCX, filterable by year and sortable in ascending/descending order; the list arrives in JSON format in a repository
- MNV Contracts: https://mnv.hu/gazdalkodas/kozbeszerzesek/szerzodeskereso JSON, includes dates, and pagination is possible

## Key features:
1. Users subscribe to "events" parameterized with keywords or phrases (even the number of an already accepted law, if there are modifications and information is required).
2. When a bulletin containing such a keyword, phrase, or regulation is uploaded, the respective user receives a notification email.
3. Unsubscribe option.
4. Since the content is in PDF format, it would be convenient for the system to directly send the relevant paragraph(s) in which the keyword appears. If it appears more than 3 times, the entire regulation/decision could be included, along with a link to the PDF.
5. Stemming should be applied to eliminate inflectional forms and variations.
6. GDPR compliance (data handling policy).

## Useful features:
1. It would be beneficial if users could subscribe to the table of contents of each bulletin, especially for legal enthusiasts. This would save them from constantly visiting the bulletin website, checking for updates, and opening the PDF.
2. Determining the upload schedule for the bulletins would be useful for timing the scraping. If there is a specific time of day, the scraping could be scheduled accordingly; otherwise, it could be checked every 1-2 hours.
3. The email could contain a link to the original bulletin website's search function for users interested in previous content.

Beyond the bulletin, additional modules could be built for other events: Court Decisions, Prosecutor's Office, National Tax and Customs Administration (NAV) anonymized decisions, Publicly Discussed Documents (kormany.hu), Submissions to the National Assembly (e.g., TV proposals).

## Architecture:
### Scraper/Event Generator:
   - Any program can be used for this task, such as scraping the Bulletin (https://onlineszamla.nav.gov.hu/informatikai_valtozasok), etc. It communicates with the Monitor via an HTTP API. It functions independently and operates based on its own logic. Technically, anyone can write an event generator if it communicates with the Monitor according to the standards. Search functionality does not need to be implemented; the Monitor takes care of it.
   
### Monitor:
   - Manages subscriptions/unsubscriptions, receives events/data from scrapers, searches within the data, and notifies subscribers. Provides a backend API for its tasks.

## Operations/Processes:
1. _Subscription to an "event":_ The type of event can be selected (known event generator programs), and parameters can be provided, initially in text form separated by semicolons (depends on the scraper).

2a. _Sending notifications:_ The Scraper (event generator) requests its specific parameters via the API, runs with those parameters, and if an event occurs, it informs the Monitor via the API, which then notifies the subscribers.

2b. _Uploading new data to the Monitor:_ The Scraper finds new data, converts it to text format, and submits it to the Monitor. The Monitor searches the new content for subscribed keywords and notifies the subscribers in case of a match.

3. _Unsubscription_: The unsubscribe link is included in the email sent by the Monitor.

## Questions:
1. How do the scraper's data connect with the notifications?
   - Through an HTTP API.
   
2. What types of keywords can be used for subscription? Free-form? Incorporation of stemming? Predefined?
   - Free-form text can be provided during subscription from the Monitor's perspective, including the rules brought by the respective Scraper (e.g., keywords separated by semicolons).

3. Approximately how many subscriptions are expected?
   - Initially, between 5 and 150. An inquiry questionnaire was filled out by 20 individuals.

4. Approximately how many notification emails need to be sent, for example, for a Bulletin scraper event?
   - Up to 200 per day. A user can subscribe to multiple keywords, but relevant content may not be available for weeks or months.

5. How should the notification emails be sent?
   - Using a Gmail or a self-hosted email account.
   - For larger quantities, services like Mailgun or similar can be utilized.

6. Are there any similar (foreign) applications?

## Resources:
     - GH Monitor backend: https://github.com/Code-for-Hungary/bmm-backend
     - GH Monitor frontend: https://github.com/Code-for-Hungary/bmm-frontend
     - Monitor frontend: http://bmmfrontend.billy.hu/
     - GH Protoscraper: https://github.com/Code-for-Hungary/bmm-protoscraper
     - Endpoint #1 (bulletin): http://bmmbackend.billy.hu/api/events/bygenerator/994966da-4cd6-420a-9597-ded3dfb85fc1
     - Endpoint #2 (protoscraper): http://bmmbackend.billy.hu/api/events/bygenerator/994966da-53ea-4797-a01d-7926c0b8053d
     - Bulletin scraper: https://github.com/zolizoli/bulletinMonotorScraper (needs sync with bmm API)
