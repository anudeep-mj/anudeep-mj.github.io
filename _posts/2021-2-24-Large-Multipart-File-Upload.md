Problem:

When you develop a service which takes an uploaded file, Multipart file is a standard format to send it. But when you call a POST on this API with a large file (>500 MB), there is a high chance that you will run into heap space issue.
How do you deal with this large file uploading?

Soln:

Use MultipartEntity to weave the file inputstream to it and send it in the POST call.

{% highlight java linenos %}

    public String scanInputStream (
        final InputStream in,
        final String fileName)
        throws IOException, GeneralSecurityException
    {
        if (logger.isDebugEnabled()) {
            logger.debug("Start scanning file: " + fileName);
        }
        final MultipartEntity entity = new MultipartEntity();
        entity.addPart("name", new StringBody(fileName));
        entity.addPart("file", new InputStreamBody(in, fileName));
        
        final HttpPost request = new HttpPost(url4Scan);
        request.setEntity(entity);

        final HttpClient client = getHttpClient();
        final HttpContext ctx = new BasicHttpContext();
        final HttpResponse response = client.execute(request, ctx);

        return readInString(response.getEntity().getContent());
    }

{% endhighlight %}
