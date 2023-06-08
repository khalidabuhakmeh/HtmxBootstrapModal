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

The modal uses Bootstrap and some of the CSS and JavaScript required to make it behave appropriately. 

Below is the markup found in `Shared/Modal.cshtml`.

```html
@model IndexModel

<div id="my-modal" class="modal fade" tabindex="-1">
    <div class="modal-dialog modal-dialog-centered">
        <div id="modal-body" class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">From The Server</h5>
                <button type="button"
                        class="btn-close"
                        data-bs-dismiss="modal"
                        aria-label="Close">
                </button>
            </div>
            <div class="modal-body">
                <form id="myForm" hx-post
                      hx-page="index"
                      hx-page-handler="Modal"
                      hx-target="closest .modal-body">
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
<script>
    function showModal() {
        const modal = new bootstrap.Modal('#my-modal');
        modal.show();
    }
    // scopes the modal so we can keep creating them
    showModal();
</script>
```

Note that the `<script>` tag will be executed as soon as the HTML block is added to the DOM.

### 3. Modal Response

When we get a response from ASP.NET Core, the HTML echoes back our message.

```html
@model IndexModel

<strong>You Said: "@Model.Message"</strong>
```

## Conclusion

In three relatively simple steps, we have created a bootstrap modal implementation using HTMX.
