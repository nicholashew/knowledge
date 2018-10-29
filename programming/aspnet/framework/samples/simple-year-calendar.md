# Simple Year Calendar Web Form

## Frontend

### CSS

```css
<style type="text/css">
    table.year-calendar td { padding:0; margin:0; border:1px solid #dadada; }
    table.year-calendar th { width:25px; text-align:center; }
    table.year-calendar td { text-align:center; }
    table.year-calendar td span { color:#dadada; }
    table.year-calendar td a { color:#000000; text-decoration:none; }
    table.year-calendar td a:hover { text-decoration:underline; }
    table.year-calendar td a.hasEvents { color:#ff0000; }
    table.year-calendar td a.selected { color:#0000ff; font-weight:bold; }
    table.year-calendar td.month { background-color:#999999; }
    table.year-calendar td.weekend { background-color:#D0D0D0; }
</style>
```

### ASPX Markup

```aspx
<table class="year-calendar table table-condensed table-hover table-striped">
    <thead>
        <tr>
            <th>
                <asp:Literal ID="litSelectedYear" runat="server" />
            </th>            
            <asp:Literal ID="litHeader" runat="server" />
        </tr>
    </thead>
    <asp:Repeater ID="repMonths" runat="server" OnInit="repMonths_OnInit" OnItemDataBound="repMonths_OnItemDataBound">
        <ItemTemplate>                  
            <tr>
                <td class="month">
                    <asp:HyperLink ID="hylMonth" runat="server" />
                </td>
                <asp:Repeater ID="repDays" runat="server" OnItemDataBound="repDays_OnItemDataBound">
                    <ItemTemplate>
                        <td id="tdDay" runat="server">
                            <asp:Literal ID="litDay" runat="server" />
                        </td>
                    </ItemTemplate>
                </asp:Repeater>
            </tr>
        </ItemTemplate>
    </asp:Repeater>
</table>
```

## Backend (C#)

```cs
using System;
using System.Web.UI.HtmlControls;
using System.Web.UI.WebControls;

public partial class _Default : System.Web.UI.Page
{
    private DateTime _dtMonth;
    private DateTime _selectedDate;
    private bool _specialDaySelected = true;
    private int _currentBindingMonth;

    protected void repMonths_OnInit(object sender, EventArgs e)
    {
        if (!DateTime.TryParse(Request.QueryString["CalDate"], out _selectedDate))
        {
            _specialDaySelected = false;
            int selectedMonth, selectedYear;
            int.TryParse(Request.QueryString["CalYear"], out selectedYear);
            int.TryParse(Request.QueryString["CalMonth"], out selectedMonth);

            if (selectedYear <= 0) selectedYear = DateTime.Now.Year;
            if (selectedMonth <= 1) selectedMonth = 1;
            else if (selectedMonth > 12) selectedMonth = 12;

            _selectedDate = new DateTime(selectedYear, selectedMonth, 1);
        }

        litSelectedYear.Text = _selectedDate.Year.ToString();

        repMonths.DataSource = new int[]{1,2,3,4,5,6,7,8,9,10,11,12};
        repMonths.DataBind();
    }

    protected void repMonths_OnItemDataBound(object sender, RepeaterItemEventArgs e)
    {
        if (e.Item.ItemType == ListItemType.Item || e.Item.ItemType == ListItemType.AlternatingItem)
        {
            Repeater repDays = (Repeater)e.Item.FindControl("repDays");
            HyperLink hylMonth = (HyperLink)e.Item.FindControl("hylMonth");
            _currentBindingMonth = (int)e.Item.DataItem;
            _dtMonth = new DateTime(_selectedDate.Year, _currentBindingMonth, 1);

            hylMonth.Text = _dtMonth.ToString("MMM");
            hylMonth.NavigateUrl = string.Format("{0}?CalMonth={1}&CalYear={2}", Request.Path, _currentBindingMonth, _selectedDate.Year);

            if (_currentBindingMonth == _selectedDate.Month) hylMonth.Attributes.Add("class", "selected");

            if (_dtMonth.DayOfWeek != DayOfWeek.Monday)
            {
                int daysToSubtract = -(int)_dtMonth.DayOfWeek;

                if (_dtMonth.DayOfWeek == DayOfWeek.Sunday) daysToSubtract = -7; // Special case. US weeks start with sunday, thus the enum DayOfWeek.Sunday = 0.

                _dtMonth = _dtMonth.AddDays(daysToSubtract + 1);
            }

            DateTime[] dates = new DateTime[37];
            for (int i = 0; i < 37; i++)
            {
                dates[i] = _dtMonth;
                _dtMonth = _dtMonth.AddDays(1);
            }

            repDays.DataSource = dates;
            repDays.DataBind();
        }
    }

    protected void repDays_OnItemDataBound(object sender, RepeaterItemEventArgs e)
    {
        if (e.Item.ItemType == ListItemType.Item || e.Item.ItemType == ListItemType.AlternatingItem)
        {
            DateTime date = (DateTime)e.Item.DataItem;
            Literal litDay = (Literal)e.Item.FindControl("litDay");
            HtmlTableCell tdDay = (HtmlTableCell)e.Item.FindControl("tdDay");

            if (date.DayOfWeek == DayOfWeek.Saturday || date.DayOfWeek == DayOfWeek.Sunday)
                tdDay.Attributes.Add("class", "weekend");

            if (_currentBindingMonth == date.Month)
                litDay.Text = string.Format("<a href=\"{0}?CalDate={3}-{2}-{1}\"{4}>{3}</a>", Request.Path, date.Year, date.Month, date.Day.ToString("D2"), (_specialDaySelected && date.Equals(_selectedDate)) ? " class=\"selected\"" : string.Empty);
            else
                litDay.Text = string.Format("<span>{0}</span>", date.Day.ToString("D2"));

            // Clear ID's
            tdDay.ID = string.Empty;
        }
    }

	private void RenderHeader()
	{
		StringBuilder sb = new StringBuilder();

		for (int iWeek = 1; iWeek <= 6; iWeek++) {
			for (int iDay = 1; iDay <= 7; iDay++) {
				if (!(iWeek == 6 & iDay > 2)) {
					sb.AppendFormat("<th>{0}</th>", GetDayName(iDay));
				}
			}
		}

		litHeader.Text = sb.ToString();

	}
	
    public static string GetDayName(int dayInWeek)
    {
        switch (dayInWeek)
        {
            case 1: return "mo";
            case 2: return "tu";
            case 3: return "we";
            case 4: return "th";
            case 5: return "fr";
            case 6: return "sa";
            case 7: return "su";
        }

        return "God only made seven days in a week.";
    }
}
```

## Backend (VB)

```vbnet
	Private _dtMonth As DateTime
    Private _selectedDate As DateTime
    Private _specialDaySelected As Boolean = True
    Private _currentBindingMonth As Integer

    Protected Sub repMonths_OnInit(sender As Object, e As EventArgs)
        If Not DateTime.TryParse(Request.QueryString("CalDate"), _selectedDate) Then
            _specialDaySelected = False
            Dim selectedMonth As Integer, selectedYear As Integer
            Integer.TryParse(Request.QueryString("CalYear"), selectedYear)
            Integer.TryParse(Request.QueryString("CalMonth"), selectedMonth)

            If selectedYear <= 0 Then
                selectedYear = DateTime.Now.Year
            End If
            If selectedMonth <= 1 Then
                selectedMonth = 1
            ElseIf selectedMonth > 12 Then
                selectedMonth = 12
            End If

            _selectedDate = New DateTime(selectedYear, selectedMonth, 1)
        End If

        litSelectedYear.Text = _selectedDate.Year.ToString()

        repMonths.DataSource = New Integer() {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12}
        repMonths.DataBind()
    End Sub

    Protected Sub repMonths_OnItemDataBound(sender As Object, e As RepeaterItemEventArgs)
        If e.Item.ItemType = ListItemType.Item OrElse e.Item.ItemType = ListItemType.AlternatingItem Then
            Dim repDays As Repeater = DirectCast(e.Item.FindControl("repDays"), Repeater)
            Dim hylMonth As HyperLink = DirectCast(e.Item.FindControl("hylMonth"), HyperLink)
            _currentBindingMonth = CInt(e.Item.DataItem)
            _dtMonth = New DateTime(_selectedDate.Year, _currentBindingMonth, 1)

            hylMonth.Text = _dtMonth.ToString("MMM")
            hylMonth.NavigateUrl = String.Format("{0}?CalMonth={1}&CalYear={2}", Request.Path, _currentBindingMonth, _selectedDate.Year)

            If _currentBindingMonth = _selectedDate.Month Then
                hylMonth.Attributes.Add("class", "selected")
            End If

            If _dtMonth.DayOfWeek <> DayOfWeek.Monday Then
                Dim daysToSubtract As Integer = -CInt(_dtMonth.DayOfWeek)

                If _dtMonth.DayOfWeek = DayOfWeek.Sunday Then
                    daysToSubtract = -7
                End If
                ' Special case. US weeks start with sunday, thus the enum DayOfWeek.Sunday = 0.
                _dtMonth = _dtMonth.AddDays(daysToSubtract + 1)
            End If

            Dim dates As DateTime() = New DateTime(36) {}
            For i As Integer = 0 To 36
                dates(i) = _dtMonth
                _dtMonth = _dtMonth.AddDays(1)
            Next

            repDays.DataSource = dates
            repDays.DataBind()
        End If
    End Sub

    Protected Sub repDays_OnItemDataBound(sender As Object, e As RepeaterItemEventArgs)
        If e.Item.ItemType = ListItemType.Item OrElse e.Item.ItemType = ListItemType.AlternatingItem Then
            Dim [date] As DateTime = DirectCast(e.Item.DataItem, DateTime)
            Dim litDay As Literal = DirectCast(e.Item.FindControl("litDay"), Literal)
            Dim tdDay As HtmlTableCell = DirectCast(e.Item.FindControl("tdDay"), HtmlTableCell)

            If [date].DayOfWeek = DayOfWeek.Saturday OrElse [date].DayOfWeek = DayOfWeek.Sunday Then
                tdDay.Attributes.Add("class", "weekend")
            End If

            If _currentBindingMonth = [date].Month Then
                litDay.Text = String.Format("<a href=""{0}?CalDate={3}-{2}-{1}""{4}>{3}</a>", Request.Path, [date].Year, [date].Month, [date].Day.ToString("D2"), If((_specialDaySelected AndAlso [date].Equals(_selectedDate)), " class=""selected""", String.Empty))
            Else
                litDay.Text = String.Format("<span>{0}</span>", [date].Day.ToString("D2"))
            End If

            ' Clear ID's
            tdDay.ID = String.Empty
        End If
    End Sub

	Private Sub RenderHeader()

        Dim sb As New StringBuilder()

        For iWeek As Integer = 1 To 6
            For iDay As Integer = 1 To 7
                If Not (iWeek = 6 And iDay > 2) Then
                    sb.AppendFormat("<th>{0}</th>", GetDayName(iDay))
                End If
            Next
        Next
       
        litHeader.Text = sb.ToString()

    End Sub

    Public Shared Function GetDayName(dayInWeek As Integer) As String
        Select Case dayInWeek
            Case 1
                Return "mo"
            Case 2
                Return "tu"
            Case 3
                Return "we"
            Case 4
                Return "th"
            Case 5
                Return "fr"
            Case 6
                Return "sa"
            Case 7
                Return "su"
        End Select

        Return "God only made seven days in a week."
    End Function
```