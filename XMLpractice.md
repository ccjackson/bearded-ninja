bearded-ninja
=============

Practice with XML sheets

*STORES THE DATA*

using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Xml.Linq;
using System.Data.SqlClient;

public partial class NewHire_NewHireBenefits : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        if (Page.IsPostBack)
        {
            Page.Validate();
            if (Page.IsValid)
            {
                //Get Values From Form
                string eName = empName.Text;
                string eID = empID.Text;
                string SSN = socialSec.Text;
                string Hired = hireDate.Text;
                string dName = deptName.Text;
                string dID = deptID.Text;
                string locale = location.Text;
                string workPhone = wokeTelephone.Text;
                string medCov = medCoverage.SelectedValue;
                string medgru = medGroup.SelectedValue;
                string illHMO = illiniHMO.SelectedValue;
                string jHMO = jersHMO.SelectedValue;
                string oHMO = otherHMO.SelectedValue;
                string dentCov = dentalCoverage.SelectedValue;
                string dentOpt = dentalOption.SelectedValue;
                string n1 = nameBox1.Text;
                string n2 = nameBox2.Text;
                string n3 = nameBox3.Text;
                string n4 = nameBox4.Text;
                string r1 = realBox1.Text;
                string r2 = realBox2.Text;
                string r3 = realBox3.Text;
                string r4 = realBox4.Text;
                string s1 = sex1.SelectedValue;
                string s2 = sex2.SelectedValue;
                string s3 = sex3.SelectedValue;
                string s4 = sex4.SelectedValue;
                string ss1 = ssn1.Text;
                string ss2 = ssn2.Text;
                string ss3 = ssn3.Text;
                string ss4 = ssn4.Text;
                string b1 = BD1.Text;
                string b2 = BD2.Text;
                string b3 = BD3.Text;
                string b4 = BD4.Text;
                string m1 = medical1.SelectedValue;
                string m2 = medical2.SelectedValue;
                string m3 = medical3.SelectedValue;
                string m4 = medical4.SelectedValue;
                string d1 = dental1.SelectedValue;
                string d2 = dental2.SelectedValue;
                string d3 = dental3.SelectedValue;
                string d4 = dental4.SelectedValue;
                

                XDocument hireForm = new XDocument(
                                    new XComment("Application Form for " + eName),
                                    new XElement("hireForm")
                                      );

                foreach (Control myContol in xmlForm.Controls)
                {

                    if (myContol.GetType().Name == "TextBox")
                    {
                        TextBox textbox = (TextBox)myContol;
                        hireForm.Element("hireForm").Add(new XElement("Field", textbox.Text, new XAttribute("ID", myContol.ID), new XAttribute("Type", "TextBox")));
                    }
                    else if (myContol.GetType().Name == "RadioButtonList")
                    {
                        RadioButtonList radiolst = (RadioButtonList)myContol;
                        hireForm.Element("hireForm").Add(new XElement("Field", radiolst.Text, new XAttribute("ID", myContol.ID), new XAttribute("Type", "RadioButtonList")));
                    }
                    else if (myContol.GetType().Name == "CheckBox")
                    {
                        CheckBox chk = (CheckBox)myContol;
                        if (chk.Checked)
                        {
                            hireForm.Element("hireForm").Add(new XElement("Field", chk.Text, new XAttribute("ID", myContol.ID), new XAttribute("Type", "CheckBox")));
                        }
                    }
                    else if (myContol.GetType().Name == "CheckBoxList")
                    {
                        CheckBoxList chklst = (CheckBoxList)myContol;
                        string values;
                        values = "";
                        for (int i = 0; i < chklst.Items.Count; i++)
                        {
                            if (chklst.Items[i].Selected)
                            {
                                values += chklst.Items[i].Value + ", ";
                            }

                        }
                        hireForm.Element("hireForm").Add(new XElement("Field", values, new XAttribute("ID", myContol.ID), new XAttribute("Type", "CheckBoxList")));



                    }
                }
                string hireFile = hireForm.ToString();

                ;


                //Establish DB connection
                SqlConnection conn = new SqlConnection("Data Source = notes.marietta.edu; Initial Catalog = ccj001; Persist Security Info = True; User ID = ccj001; Password = Cwood0401789");

                try
                {
                    conn.Open();
                    conn.ChangeDatabase("ccj001");

                    //construct SQL query
                    string sqlQuery = "INSERT INTO NewHireXML VALUES('" +
                                   eName + "','" +
                                   eID + "','" +
                                   SSN + "','" +
                                   Hired + "','" +
                                   dName + "','" +
                                   dID + "','" +
                                   locale + "','" +
                                   workPhone + "','" +
                                   hireFile + "')";
                    SqlCommand sqlComm = new SqlCommand(sqlQuery, conn);
                    sqlComm.ExecuteNonQuery();

                    Confirmation.Text = "Your form has been successfully submitted!<br>" +
                            "<a href=\"NewHireDisplay.aspx\">Click here</a> to view the results.";


                }
                catch (SqlException exception)
                {
                    ErrMsg.Text = "Error No: " + exception.Number + " | Error Message: " + exception.Message;
                    throw;
                }
                //close connection
                conn.Close();


            }
        }        
    }
}





*DISPLAYS DATA*


using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Xml.Linq;
using System.Data.SqlClient;



public partial class NewHire_NewHireDisplay : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        string strposition = "";
        string strintDate = "";
        //string strgen = "";
        //Establish DB connection
        SqlConnection conn = new SqlConnection("Data Source = notes.marietta.edu; Initial Catalog = ccj001; Persist Security Info = True; User ID = ccj001; Password = Cwood0401789");

        try
        {
            conn.Open();
            conn.ChangeDatabase("ccj001");

            //construct SQL query
            string sqlQuery = "SELECT * FROM NewHireXML";

            SqlCommand sqlComm = new SqlCommand(sqlQuery, conn);
            SqlDataReader rsNewHireXML = sqlComm.ExecuteReader();

            string resultHTML = "<table width='100%'border='1'><tr> <td>Employee Name</td> <td>Employee ID</td> <td>SSN</td> <td>Hire Date</td> <td>Department Name</td> <td>Department ID</td> <td>Location</td> <td>Work Telephone</td> <td>Medical Group</td> <td>Dental Option</td></tr>";
            if (rsNewHireXML.Read())
            {
                do
                {
                    XDocument doc = XDocument.Parse(rsNewHireXML["HireXML"].ToString());


                    var field = from Field in doc.Descendants("Field")
                                where Field.Attribute("ID").Value == "medGroup"
                                select new
                                {
                                    Position = Field.Value,
                                };

                    foreach (var s in field)
                    {
                        strposition = s.Position.ToString();
                    };

                    field = from Field in doc.Descendants("Field")
                            where Field.Attribute("ID").Value == "dentalOption"
                            select new
                            {
                                Position = Field.Value,
                            };

                    foreach (var s in field)
                    {
                        strintDate = s.Position.ToString();
                    };

                    field = from Field in doc.Descendants("Field")
                            where Field.Attribute("ID").Value == "dentalOption"
                            select new
                            {
                                Position = Field.Value,
                            };

                    foreach (var s in field)
                    {

                        strintDate = s.Position.ToString();

                    };

                    /*field = from Field in doc.Descendants("Field")
                            where Field.Attribute("ID").Value == "gender"
                            select new
                            {
                                Position = Field.Value,
                            };

                    foreach (var s in field)
                    {

                        strgen = s.Position.ToString();

                    };*/

                    //SQL Commands

                    resultHTML += "<tr><td>" + rsNewHireXML["EmpName"] + "</td>" +
                                    "<td>" + rsNewHireXML["EmpID"] + "</td>" +
                                    "<td>" + rsNewHireXML["SocialSec"] + "</td>" +
                                    "<td>" + rsNewHireXML["HireDate"] + "</td>" +
                                    "<td>" + rsNewHireXML["DeptName"] + "</td>" +
                                    "<td>" + rsNewHireXML["DeptID"] + "</td>" +
                                    "<td>" + rsNewHireXML["Locale"] + "</td>" +
                                    "<td>" + rsNewHireXML["WorkPhone"] + "</td>" +
                                    "<td>" + strposition + "</td>" +
                                    "<td>" + strintDate + "</td></tr>";
                                    //"<td>" + strgen + "</td> </tr>";
                } while (rsNewHireXML.Read());


                resultHTML += "</table>";

                hireLabel.Text = resultHTML;
            }
            else
            {
                hireLabel.Text = "No records found!";
            }

        }
        catch (SqlException exception)
        {
            hireLabel.Text = "Error No: " + exception.Number + " | Error Message: " + exception.Message;
            throw;
        }     



        //Close DB Connection (IMPORTANT!!)
        conn.Close();
    }
}
