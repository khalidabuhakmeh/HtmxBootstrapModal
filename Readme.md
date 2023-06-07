# HTMX and Bootstrap Modals

This repository includes a sample showing HTMX used with ASP.NET Core to display bootstrap modals from the server. This project uses the HTMX.NET project to make use of ASP.NET Core TagHelpers.

## Getting Started

You'll need .NET 7 SDK installed. Once installed, you can run `dotnet run` on the HtmxBootstrapModal web project.

## Points of Interest

There are three parts to showing a modal from the server:

### 1. The Host Page

The host page, in this case it's `Index.cshtml`, requires a trigger to make a request to the server. We also need to place the modal's HTML into a parent container.

```html
<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <button hx-get hx-page="Index" 
            hx-page-handler="Modal"
            hx-target="#modal-container">
        Show Modal
    </button>
</div>

<div id="modal-container"></div>
```

When you click the `button`, the client makes a request for the modal HTML from the server and places it in the `#modal-container` element.

What does the modal HTML look like?

### 2. Modal HTML

The modal uses Bootstrap and some of the CSS required to make it behave appropriately. That said, you might want to use the JavaScript provided by Boostrap and process the modal using HTMX JavaScript. I find the approach in this sample to be easier as HTMX automatically processes HTML from the server to register events and triggers.

Below is the markup found in `Shared/Modal.cshtml`.

```html
<div class="modal-group">
    <div class="modal show fade" tabindex="-1" style="display:block">
        <div class="modal-dialog modal-dialog-centered">
            <div id="modal-body" class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">From The Server</h5>
                    <button type="button"
                            class="btn-close"
                            data-bs-dismiss="modal"
                            aria-label="Close"
                            onclick="this.closest('.modal-group').remove()">
                    </button>
                </div>
                <div class="modal-body">
                    <form id="myForm" hx-post
                          hx-page="index"
                          hx-page-handler="Modal"
                          hx-target="#modal-body">
                        <div class="mb-3">
                            <label asp-for="Message" class="form-label"></label>
                            <input asp-for="Message" class="form-control" placeholder="Your message...">
                        </div>
                        @Html.AntiForgeryToken()
                    </form>
                </div>
                <div class="modal-footer">
                    <button form="myForm"
                            type="submit"
                            class="btn btn-primary">
                        Save changes
                    </button>
                </div>
            </div>
        </div>
    </div>
    <div class="modal-backdrop fade show"></div>
</div>
```
Note the following aspects:

1. We need `display:block` on the element. Without this, the modal won't be visible.
2. The `submit` button uses the `form` attribute to trigger the HTMX-powered form.
3. We're going to replace the entire contents of the modal

### 3. Modal Response

When we get a response from ASP.NET Core, the HTML echoes back our message.

```html
<div class="modal-header">
    <h5 class="modal-title">From The Server</h5>
    <button type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
            onclick="this.closest('.modal-group').remove()">
    </button>
</div>
<div id="modal-body" class="modal-body">
    <strong>You Said: "@Model.Message"</strong>
</div>
```

## Conclusion

In three relatively simple steps, we have created a bootstrap modal implementation using HTMX.
