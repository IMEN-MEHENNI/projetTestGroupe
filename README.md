# test exam cssdm
################################################################## calculatrice:
###########About.cshtml##########
@{
    ViewBag.Title = "About";
}
<h2>@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3>

<form action="/Home/About" method="post" name="calcule">
    <fieldset>
        <div style="padding-right: 0px; margin-right:0px; float:left;" ">
            <input type="number" name="firstNumber"/>
        </div>
        <div style="padding-right: 0px;margin-right: 0px;float: left;">
            <p>+</p>
        </div>
        <div style="padding-right: 0px;margin-right: 0px;float: left;" >
            <input type="number" name="secondNumber"/>
        </div>
        <div class="col-md-1" style="padding-left: 0px;margin-left: 15px;">
            <button class="btn btn-primary btn-sm" type="submit" style="height: 25px;">résultat</button>
        </div>
    </fieldset>
</form>


<p>@ViewBag.result</p>

###########Controller.cs##########

public ActionResult About()
        {
            var first = Request.Form["firstNumber"];
            var second = Request.Form["secondNumber"];
         if(first != null && second != null && first != "" && second != "" )
             ViewBag.result = float.Parse(first) + float.Parse(second);
          else
                ViewBag.result = "veuillez saisir les deux chiffres";
            ViewBag.Message = "Your application description page.";

            return View();
        }
        
        
 ################################################################## select:
 
 ###########Controller.cs##########
 private bdfilmsEntities db = new bdfilmsEntities();
        string cat = "";
        string chaineconnexion = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=bdfilms;Integrated Security=true";
        public ActionResult Index()
        {
            DataTable listed = new DataTable();
            using (SqlConnection con = new SqlConnection(chaineconnexion))
            {
                
                con.Open();

                SqlDataAdapter adp = new SqlDataAdapter("select distinct films.categorie from films ", con);
                adp.Fill(listed);
            }

            Dictionary<string, string> MyList = new Dictionary<String, String>();
            for (int i = 0; i < listed.Rows.Count; i++)
                {
                MyList.Add(i.ToString(), listed.Rows[i][0].ToString());
                }
            
            

            ViewData["cate"] = MyList;
            
            DataTable liste = new DataTable(); 
            if (Request.Form["identifiant"] == null)
            {
                using (SqlConnection con = new SqlConnection(chaineconnexion))
                {
                    con.Open();

                    SqlDataAdapter adp = new SqlDataAdapter("select * from films ", con);
                    adp.Fill(liste);
                }

            }
            else
            {
                cat = Request.Form["identifiant"];

                if (cat == "")
                {
                    using (SqlConnection con = new SqlConnection(chaineconnexion))
                    {
                        con.Open();
                        SqlDataAdapter adp = new SqlDataAdapter("select * from films ", con);
                        adp.Fill(liste);
                    }
                    ViewBag.number = "La liste des films";
                }
                else
                {
                    using (SqlConnection con = new SqlConnection(chaineconnexion))
                    {
                        con.Open();
                        string choix = "select * from films where films.categorie = " + "'" + cat + "'";
                        SqlDataAdapter adp = new SqlDataAdapter(choix, con);
                        adp.Fill(liste);
                    }
                    string TitreTableau = "Catégorie :" + cat;
                    ViewBag.number = TitreTableau;
                }
            }
            return View(liste);
        }
 
 ###########Index.cshtml##########
 @model System.Data.DataTable
@{
    ViewBag.Title = "Home Page";
    var list = ViewData["cate"] as Dictionary<String, String>;
}

<div class="jumbotron">
    <h1>ASP.NET</h1>
    <p class="lead">ASP.NET is a free web framework for building great Web sites and Web applications using HTML, CSS and JavaScript.</p>
    <p><a href="https://asp.net" class="btn btn-primary btn-lg">Learn more &raquo;</a></p>
</div>
<form action="/Home/Index" method="post" name="identifiant">
    <fieldset>
        <div style="padding-right: 0px; margin-right:0px;" class=" col-md-6">
            <select id="identifiant" name="identifiant" class="form-control" style="height: 40px;">
                <option>choisir une categorie</option>
                <option value="">toutes les categorie</option>
                @if (list != null)
                {
                    foreach (KeyValuePair<string, string> categorie in list)
                    {
                        <option>@categorie.Value</option>
                    }

                }


            </select>
        </div>
        <div class="col-md-1" style="padding-left: 0px;margin-left: 0px;">
            <button class="btn btn-primary btn-lg" title="Filtrer les films par catégorie" type="submit" style="height: 40px;"><i class="glyphicon glyphicon-filter"></i></button>
        </div>
    </fieldset>
</form>
<div class="row">
    <table class="table table-bordered table-striped">
        <tr>
            <th>Identifiant du film</th>
            <th>Titre du film</th>
            <th>Realisateur du film</th>
            <th>Durée du film</th>
            <th>Categorie du film</th>
            <th>Actions</th>
        </tr>
        @for (int i = 0; i < Model.Rows.Count; i++)
        {
            <tr>
                <td>@Model.Rows[i][0]</td>
                <td>@Model.Rows[i][1]</td>
                <td>@Model.Rows[i][2]</td>
                <td>@Model.Rows[i][3]</td>
                <td>@Model.Rows[i][4]</td>
                <td style="text-align:center;">
                </td>
            </tr>
        }
    </table>
</div>
