# Adding Items

The museum will display items on the front page, but we'll use a separate administrative page to add items.

Let's start building the administrative page.

## Front End HTML

To add items on the front end, edit `admin.html` so it contains this:

```
  <h1>Museum of Ordinary Objects: The Admin Page!</h1>
    <div class="heading">
      <div class="circle">1</div>
      <h2>Add an Item</h2>
    </div>
    <div class="add">
      <div class="form">
        <input v-model="title" placeholder="Title">
        <p></p>
        <input type="file" name="photo" @change="fileChanged">
        <button @click="upload">Upload</button>
      </div>
      <div class="upload" v-if="addItem">
        <h2>{{addItem.title}}</h2>
        <img :src="addItem.path" />
      </div>
    </div>
```

Every item in the museum will have two properties -- a title and a photo. So we create one input for entering the title of the object and another input for uploading a file with a photo.

We bind the title to a `title` model, for two-way data binding. We add an event handler for the `change` event to the input for uploading a file. This lets us keep track of which file the user selected. We also have an event handler for the `click` event on an upload button, so that we can upload the photo to our server.

Below this form we have a div that will show the title of the item and the photo of the item that we uploaded. This will display only after the item is successfully uploaded to the server.

The CSS to make this look nice is already included for you.

## Front End JavaScript

The HTML page in `admin.html` uses `admin.js` for the Vue code. Let's edit `admin.js` and add the following data:

```
  data: {
    title: "",
    file: null,
    addItem: null,
  }
```

and the following methods:

```
  methods: {
    fileChanged(event) {
      this.file = event.target.files[0]
    },
    async upload() {
      try {
        const formData = new FormData();
        formData.append('photo', this.file, this.file.name)
        let r1 = await axios.post('/api/photos', formData);
        let r2 = await axios.post('/api/items', {
          title: this.title,
          path: r1.data.path
        });
        this.addItem = r2.data;
      } catch (error) {
        console.log(error);
      }
    },
  }
```

In the `fileChanged` event handler, we use the event passed to us to find the name of the file that the user selected, and we store this in `file`.

In the `upload` event handler, we create a `FormData` object and append some information to it. This is built in to JavaScript and is what we use to send a file to the server. This information includes the property `photo` that the server will use to get the file from the form, the contents of the file, and the file name.

We then post to `/api/photos` and include the `FormData` object.

Note that we use `await` to wait for this to finish, and we thus have made `upload` an `async` method. The return value contains the path on the server where the file is stored.

Once this is done, we post to `/api/items` to create the item in our museum, which includes the title and the path to the photo. We again use `await` here.

Once this is done, we get back a response that contains the item we added, so we store it in `addItem` and use that to display the item on the admin page, showing the user that the upload was successful.

## Back End -- Uploading Photos



## Testing

You will need to quit and restart the server:

```
firebase serve
```


