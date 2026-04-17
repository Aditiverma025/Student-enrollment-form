# Student-enrollment-form
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Student Enrollment Form</title>

    <!-- Bootstrap -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

    <!-- JsonPowerDB -->
    <script src="https://login2explore.com/jpdb/resources/js/0.0.3/jpdb-commons.js"></script>
</head>

<body class="bg-light">

<div class="container mt-5">
    <h2 class="text-center">Student Enrollment Form</h2>

    <form id="studentForm">

        <div class="mb-3">
            <label>Roll No</label>
            <input type="text" id="rollNo" class="form-control" onblur="checkStudent()">
        </div>

        <div class="mb-3">
            <label>Full Name</label>
            <input type="text" id="fullName" class="form-control" disabled>
        </div>

        <div class="mb-3">
            <label>Class</label>
            <input type="text" id="stuClass" class="form-control" disabled>
        </div>

        <div class="mb-3">
            <label>Birth Date</label>
            <input type="date" id="birthDate" class="form-control" disabled>
        </div>

        <div class="mb-3">
            <label>Address</label>
            <input type="text" id="address" class="form-control" disabled>
        </div>

        <div class="mb-3">
            <label>Enrollment Date</label>
            <input type="date" id="enrollDate" class="form-control" disabled>
        </div>

        <div class="text-center">
            <button type="button" id="saveBtn" class="btn btn-success" onclick="saveData()" disabled>Save</button>
            <button type="button" id="updateBtn" class="btn btn-warning" onclick="updateData()" disabled>Update</button>
            <button type="button" id="resetBtn" class="btn btn-secondary" onclick="resetForm()" disabled>Reset</button>
        </div>

    </form>
</div>

<script>
    const connToken = "909352761-31949235947552738|90958572";
    const dbName = "SCHOOL-DB";
    const relName = "STUDENT-TABLE";

    function resetForm() {
        $("#studentForm")[0].reset();
        $("input:not(#rollNo)").prop("disabled", true);
        $("#saveBtn, #updateBtn, #resetBtn").prop("disabled", true);
        $("#rollNo").focus();
    }

    function enableFields() {
        $("input:not(#rollNo)").prop("disabled", false);
        $("#resetBtn").prop("disabled", false);
    }

    function validate() {
        return $("#rollNo").val() &&
               $("#fullName").val() &&
               $("#stuClass").val() &&
               $("#birthDate").val() &&
               $("#address").val() &&
               $("#enrollDate").val();
    }

    function checkStudent() {
        let roll = $("#rollNo").val();
        if (roll === "") return;

        let req = createGET_BY_KEYRequest(connToken, dbName, relName, JSON.stringify({"Roll-No": roll}));

        jQuery.ajaxSetup({async: false});
        let res = executeCommandAtGivenBaseUrl(req, "http://api.login2explore.com:5577", "/api/irl");
        jQuery.ajaxSetup({async: true});

        if (res.status === 400) {
            enableFields();
            $("#saveBtn").prop("disabled", false);
            $("#fullName").focus();
        } else {
            let data = JSON.parse(res.data).record;

            $("#fullName").val(data["Full-Name"]);
            $("#stuClass").val(data.Class);
            $("#birthDate").val(data["Birth-Date"]);
            $("#address").val(data.Address);
            $("#enrollDate").val(data["Enrollment-Date"]);

            enableFields();
            $("#updateBtn").prop("disabled", false);
        }
    }

    function saveData() {
        if (!validate()) {
            alert("Fill all fields!");
            return;
        }

        let jsonStr = JSON.stringify({
            "Roll-No": $("#rollNo").val(),
            "Full-Name": $("#fullName").val(),
            "Class": $("#stuClass").val(),
            "Birth-Date": $("#birthDate").val(),
            "Address": $("#address").val(),
            "Enrollment-Date": $("#enrollDate").val()
        });

        let req = createPUTRequest(connToken, jsonStr, dbName, relName);

        jQuery.ajaxSetup({async: false});
        executeCommandAtGivenBaseUrl(req, "http://api.login2explore.com:5577", "/api/iml");
        jQuery.ajaxSetup({async: true});

        alert("Student Saved!");
        resetForm();
    }

    function updateData() {
        if (!validate()) {
            alert("Fill all fields!");
            return;
        }

        let jsonStr = JSON.stringify({
            "Roll-No": $("#rollNo").val(),
            "Full-Name": $("#fullName").val(),
            "Class": $("#stuClass").val(),
            "Birth-Date": $("#birthDate").val(),
            "Address": $("#address").val(),
            "Enrollment-Date": $("#enrollDate").val()
        });

        let req = createUPDATERecordRequest(connToken, jsonStr, dbName, relName, $("#rollNo").val());

        jQuery.ajaxSetup({async: false});
        executeCommandAtGivenBaseUrl(req, "http://api.login2explore.com:5577", "/api/iml");
        jQuery.ajaxSetup({async: true});

        alert("Student Updated!");
        resetForm();
    }

    $(document).ready(function () {
        resetForm();
    });
</script>

</body>
</html>
