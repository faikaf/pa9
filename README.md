# pa9
9. Develop a custom JSP tag which accepts 10 numbers from user and sorts them in
specified order.
SortNumbersTag.java
package customtags;
import java.io.IOException;
import java.util.Arrays;
import jakarta.servlet.jsp.tagext.TagSupport;
import jakarta.servlet.jsp.JspException;
import jakarta.servlet.jsp.JspWriter;
public class SortNumbersTag extends TagSupport {
 private String numbers; // Input numbers as a comma-separated string
 private String order; // Sorting order: "asc" or "desc"
 // Setter methods
 public void setNumbers(String numbers) {
 this.numbers = numbers;
 }
 public void setOrder(String order) {
 this.order = order;
 }
 @Override
 public int doStartTag() throws JspException {
 if (numbers == null || numbers.trim().isEmpty()) {
 return SKIP_BODY;
 }
 String[] numArray = numbers.split(",");
 int[] intArray = new int[numArray.length];
 try {
 for (int i = 0; i < numArray.length; i++) {
 intArray[i] = Integer.parseInt(numArray[i].trim());
 }
 } catch (NumberFormatException e) {
 throw new JspException("Invalid number format!", e);
 }
 // Sorting logic
 Arrays.sort(intArray);
 if ("desc".equalsIgnoreCase(order)) {
 for (int i = 0; i < intArray.length / 2; i++) {
 int temp = intArray[i];
 intArray[i] = intArray[intArray.length - 1 - i];
 intArray[intArray.length - 1 - i] = temp;
 }
 }
 // Output sorted numbers
 JspWriter out = pageContext.getOut();
 try {
 out.print(Arrays.toString(intArray));
 } catch (IOException e) {
 throw new JspException("Error in SortNumbersTag", e);
 }
 return SKIP_BODY; // No body content required
 }
}
sortNumbers.tld
<?xml version="1.0" encoding="UTF-8"?>
<taglib version="2.1" xmlns="http://java.sun.com/xml/ns/javaee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
http://java.sun.com/xml/ns/javaee/web-jsptaglibrary_2_1.xsd">
 <tlib-version>1.0</tlib-version>
 <tag>
 <name>sortNumbers</name>
 <tag-class>customtags.SortNumbersTag</tag-class>
 <body-content>empty</body-content>
 <attribute>
 <name>numbers</name>
 <required>true</required>
 <rtexprvalue>true</rtexprvalue>
 </attribute>
 <attribute>
 <name>order</name>
 <required>false</required>
 <rtexprvalue>true</rtexprvalue>
 </attribute>
 </tag>
</taglib>
Index.jsp
<%@ taglib prefix="c" uri="/WEB-INF/tlds/sortNumbers.tld" %>
<html>
<head>
 <title>Sort Numbers Custom Tag</title>
</head>
<body>
 <h2>Enter 10 Numbers (comma-separated)</h2>
 <form method="post">
 <input type="text" name="numbers" required>
 <select name="order">
 <option value="asc">Ascending</option>
 <option value="desc">Descending</option>
 </select>
 <input type="submit" value="Sort">
 </form>
 <%
 String numbers = request.getParameter("numbers");
 String order = request.getParameter("order");
 if (numbers != null && !numbers.isEmpty()) {
 %>
 <h3>Sorted Numbers:</h3>
 <c:sortNumbers numbers="<%= numbers %>" order="<%= order %>" />
 <% } %>
</body>
</html>
