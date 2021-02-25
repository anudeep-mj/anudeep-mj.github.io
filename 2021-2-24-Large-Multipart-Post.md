So here is an interesting issue. Say you have to pass a large file (like 2 gigs) to a POST call which accepts a Multipart file. What do you do ?

If you pass the whole file, your service is gonna end up with heap space issues. 

Answer: Stream the file using MultipartEntity.

Using Multipart entity you can weave the body and pass the inputstream of the object in place of the file object.

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
