---
id: 29
title: 'Getting iframe’s parent window url in ASP.NET'
date: '2012-06-22T20:22:57+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=29'
permalink: /asp-net-server-side-how-to-get-iframes-parent-window-url/
categories:
    - 'Blog Posts'
tags:
    - ASP.NET
---

In Asp.Net server sided button’s click event, I somehow needed to find parent window url of an iframe, which means the url you see in the address bar of your web browser.

Let’s think of 2 pages: **Parent.aspx** and **Child.aspx**. **Child.aspx** is loading to an iframe in **Parent.aspx**. If you have a button in **Child.aspx**, you will be getting “Child.aspx” result when you use **Request.Url** method in this button’s click event.

But you see “Parent.aspx” in the address bar, right? So how to get it?

Here you need to work with client side peacefully. Otherwise the server will keep giving you the same result.

First, place a button running at server, add a click event and a client-click event.

```
<asp:Button ID="btnFindParentUrl" runat="server" Text="Get Url!" OnClick="btnFindParentUrl_Click" OnClientClick="fillHidden();"></asp:Button>
```

Put a hidden textbox inside a div with **style=”display: none;”** (not **Visible=”false”** for button because if you do client can’t see and fill it):

```
<div style="display: none;">
    <asp:TextBox ID="txtHiddenUrlField" runat="server" BorderStyle="None" Font-Size="0px" ForeColor="#F6F6F6" Height="0px" Width="0px"></asp:TextBox>
</div>
```

Now place javascript code of **fillHidden()** function:

```
<script type="text/javascript">
    function fillHidden() {
        document.getElementById('<%= txtHiddenUrlField.ClientID %>').value = parent.document.location.href;
    };
</script>
```

That’s all you have to do at client side.  
Let’s go to the server:

```
protected void btnFindParentUrl_Click(object sender, EventArgs e)
{
    string parentUrl = txtHiddenUrlField.Text;
}
```

This way, you should be getting parent url from button in an iframe.
