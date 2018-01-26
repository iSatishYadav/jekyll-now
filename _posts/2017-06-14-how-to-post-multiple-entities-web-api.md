---
layout: post
title: How to post multiple entities Web API
---
I'll keep this post short and to the point. 
Here's the Server Side code for a Web API Contoller:

````

[ResponseType(typeof(Customer))]
public async Task<IHttpActionResult> PostCustomer(IEnumerable<Customer> customers)
{
  	if (!ModelState.IsValid)
	  {
		    return BadRequest(ModelState);
  	}
	  db.Customers.AddRange(customers);
  	await db.SaveChangesAsync();
	  return StatusCode(HttpStatusCode.Created);
}

//And here's how to call this:

public async Task<string> PostMultipleCustomers()
{
  var customers = new List<Customer>
  {
    new Customer { Name = "John Doe" }, 
    new Customer { Name = "Jane Doe" }
  };
  using (var client = new HttpClient())
  {
    HttpResponseMessage response = await client.PostAsJsonAsync("http://<Url>/api/Customers", customers);
    if (response.IsSuccessStatusCode)
    {
      var result = await response.Content.ReadAsStringAsync();
      return result;
    }
  return response.StatusCode.ToString();
  }
 }
````