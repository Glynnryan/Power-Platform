# Scenario
I have several Power Automate flows that create items in various Microsoft Lists within the same SharePoint site using a service account. As a result, these list items have a "Created By" value that reflects the service account's name.

While this is acceptable in some instances, there are cases where I need a specific individual to appear as the author of the item. For example, when a Microsoft Form triggers my flow, I may want the list item to reflect the form responder as the list item author.

After some thought, I did a bit of [Google searching](https://letmegooglethat.com/?q=microsoft+list+change+created+by) and ultimately found a helpful post in the Microsoft Community titled "[Update 'Created By' and 'Modified By' fields.](https://techcommunity.microsoft.com/t5/power-apps-and-power-automate-in/update-created-by-and-modified-by-fields/m-p/3672675/highlight/true#M6072)", with an answer by [Rob Elliott](https://techcommunity.microsoft.com/t5/user/viewprofilepage/user-id/174092#profile) that gave me the framework for my solution.

# Solution

![Solution Overview](https://github.com/Glynnryan/Power-Platform/blob/main/Power%20Autoamte/Update%20Created%20By%20(Microsoft%20List)/Overview.png?raw=true)

> [!NOTE] 
> My solution uses a flow created within a Solution in Power Automate. You can still achieve the end goal by following the steps below in a dedicated flow. However, if you're not using solutions, I would recommend starting to do so, as there are several benefits, such as running a child flow.  
> For more information on Solutions, you can click [here](https://learn.microsoft.com/en-us/power-automate/overview-solution-flows).

## Steps
1. Sign in to [Power Automate](https://make.powerautomate.com/).
2. In the left-hand menu, select **Solutions**.
3. Choose the solution where you want to create the automation, or [create a new solution](https://learn.microsoft.com/en-us/power-automate/overview-solution-flows).
4. Create a new automation:
    - In the top menu, select **New** > **Automation** > **Cloud Flow** > **Instant**.
    - Enter a **Flow name**. (I called mine "Update Created By". Original, right? 🤓)
    - Select **Manually trigger a flow** as the trigger.
    - Select **Create**.
5. Select **Manually trigger a flow** and add the following inputs:

|  Type  |       Name       |                 Description                  |
| ------ | ---------------- | -------------------------------------------- |
|  Text  |    List Name     |          Name of the list to update          |
| Number |     List ID      |            List item ID to update            |
|  Text  | Created By Email | Email address of the person to set as Author |
|  Text  |   Site Address   |            Site address of the list          |

6. Add a **Send an HTTP request to SharePoint** action.
> [!TIP] 
> You can search for "HTTP" in the search box to find this action quickly.

7. Configure the action as follows:

### Site Address
Select the **Site Address** dynamic content created in step 5, or enter the relevant SharePoint site address.
> [!TIP] 
> If you want this automation to work dynamically across multiple SharePoint sites, add the **Site Address** at step 5. Alternatively, you can set this as a fixed value if your use case means the SharePoint site will not change.

### Method
Post

### Uri

``` HTML
_api/web/lists/getbytitle('@{triggerBody()?'text'}')/items('@{triggerBody()?'number'}')/validateUpdateListItem
```
> [!NOTE]
> If you've followed the exact sequence in step 5, you can use the provided Uri without modification. Otherwise, adjust as needed.

### Body

``` JSON
{
	"formValues":[
		{
			"FieldName": "Author",
			"FieldValue": "[{'Key':'i:0#.f|membership|@{triggerBody()?['text_1']}'}]"
		}
	]
}
```
> [!NOTE]
> If you've followed the exact sequence in step 5, you can use the provided JSON without modification. Otherwise, adjust as needed.

8. Add a **Respond to a Power App or flow** action.
> [!TIP]
> Add an **Output**, with the **Type** of **Text** and **Value** of "Complete" to parse this status back to your previous flow.

And that’s it! Now, whenever you need to update the “Created By” field in a Microsoft List item, use the **Run a Child Flow** action, select this automation, and input your dynamic content into the fields you set up in step 5.
