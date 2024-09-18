# Scenario
I have several Power Automate flows that create items in various Microsoft Lists within the same SharePoint site using a service account. As a result, these list items have a “Created By” value that reflects the service account’s name.

While this is acceptable in some instances, there are cases where I need a specific individual to appear as the author of the item. For example, when a Microsoft Form triggers my flow, I may want the list item to reflect the form responder as the list item author.

After some thought, I did a bit of [Google searching](https://letmegooglethat.com/?q=microsoft+list+change+created+by) and ultimately found a helpful post in the Microsoft Community titled “[Update ‘Created By’ and ‘Modified By’ fields.](https://techcommunity.microsoft.com/t5/power-apps-and-power-automate-in/update-created-by-and-modified-by-fields/m-p/3672675/highlight/true#M6072)”, with an answer by [Rob Elliott](https://techcommunity.microsoft.com/t5/user/viewprofilepage/user-id/174092#profile) that gave me the framework for my solution.
