Converted-Code - C# to Entity Framework - BEFORE & AFTER


Before

       [TestMethod]
              public void SRSTransactiondataCleanTest()
              {
                 using (SqlConnection sqlConn = new
                     SqlConnection(@"Data Source=localhost;Initial Catalog=DB;Integrated Security=SSPI;"))
                 {

                StringBuilder str = new StringBuilder();
                str.Append(@"SELECT * FROM [dbo].[SRS_Ticket_Transaction_Stage_Cleaned]");
                sqlConn.Open();
                SqlDataAdapter da = new SqlDataAdapter(str.ToString(), sqlConn);
                DataTable dResults = new DataTable();
                DataTable dAudit = new DataTable();
                da.Fill(dResults);
                
                da.SelectCommand.CommandText = @"SELECT [Table_Name],[Package_Name],[Execution_Start_Time],[Execution_End_Time],[Processing_Successful], Audit_Key," +
                    @"[Table_Initial_Row_Count],[Table_Final_Row_Count] FROM [SRS_Dim_Audit] WHERE Package_Name LIKE 'SAPSRSDataToStageClean'";

                da.Fill(dAudit);
                var r = dAudit.Rows[0][2].ToString();
                
                Assert.AreEqual(1, dAudit.Rows.Count);
                Assert.AreEqual("SRS_Ticket_Transaction_Stage_Cleaned", dAudit.Rows[0][0].ToString());
                Assert.AreEqual("SAPSRSDataToStageClean", dAudit.Rows[0][1].ToString());
                Assert.AreEqual(bool.TrueString, dAudit.Rows[0][4].ToString());
                Assert.IsTrue(int.Parse(dAudit.Rows[0][7].ToString())> 10 );
                Assert.IsTrue(dResults.Rows.Count>50);
                Assert.AreEqual(int.Parse(dAudit.Rows[0][7].ToString()), dResults.Rows.Count);
                
                sqlConn.Close();
            }
        }
       
        [TestMethod]
        public void SRSLifeCycleDataCleanTest()
        {
            using (SqlConnection sqlConn = new
               SqlConnection(@"Data Source=localhost;Initial Catalog=DB;Integrated Security=SSPI;"))
            {
                
                StringBuilder str = new StringBuilder();
                str.Append(@"SELECT * FROM dbo.SRS_Ticket_LifeCycle_Stage_Cleaned WHERE Ticket_Number LIKE '0100034236'");
                sqlConn.Open();
                SqlDataAdapter da = new SqlDataAdapter(str.ToString(), sqlConn);
                DataTable dResults = new DataTable();
                DataTable dAudit = new DataTable();
                da.Fill(dResults);

                int row = 0;
                
                Assert.AreEqual("0100034236", dResults.Rows[row][0]);
                Assert.AreEqual("E0001", dResults.Rows[row][1]);
                Assert.AreEqual(DateTime.Parse("2012-12-01 08:57:16"), dResults.Rows[row][3]);
                Assert.AreEqual(DateTime.Parse("2012-12-01 08:57:16"), dResults.Rows[row][4]);
                Assert.AreEqual(DateTime.Parse("2012-12-03 16:25:47"), dResults.Rows[row][5]);
                Assert.AreEqual((DateTime.Parse("2012-12-03 16:25:47") - DateTime.Parse("2012-12-01 08:57:16")).TotalSeconds, 
                    int.Parse(dResults.Rows[row][6].ToString()));
                Assert.AreEqual("SGILDER", dResults.Rows[row][7]);

                row++;
                Assert.AreEqual("0100034236", dResults.Rows[row][0]);
                Assert.AreEqual("E0002", dResults.Rows[row][1]);
                Assert.AreEqual(DateTime.Parse("2012-12-03 16:25:47.000"), dResults.Rows[row][3]);
                Assert.AreEqual(DateTime.Parse("2012-12-03 16:25:47.000"), dResults.Rows[row][4]);
                Assert.AreEqual(DateTime.Parse("2012-12-11 10:01:32.000"), dResults.Rows[row][5]);
                Assert.AreEqual((DateTime.Parse("2012-12-11 10:01:32.000") - DateTime.Parse("2012-12-03 16:25:47.000")).TotalSeconds,
                    int.Parse(dResults.Rows[row][6].ToString()));
                Assert.AreEqual("SPLAXTON", dResults.Rows[row][7]);

                row++;
                Assert.AreEqual("0100034236", dResults.Rows[row][0]);
                Assert.AreEqual("E0007", dResults.Rows[row][1]);
                Assert.AreEqual(DateTime.Parse("2012-12-11 10:01:32"), dResults.Rows[row][3]);
                Assert.AreEqual(DateTime.Parse("2012-12-11 10:01:32"), dResults.Rows[row][4]);
                Assert.AreEqual(DateTime.Parse("2012-12-18 00:01:01"), dResults.Rows[row][5]);
                Assert.AreEqual((DateTime.Parse("2012-12-18 00:01:01") - DateTime.Parse("2012-12-11 10:01:32")).TotalSeconds,
                    int.Parse(dResults.Rows[row][6].ToString()));
                Assert.AreEqual("SPLAXTON", dResults.Rows[row][7]);

                row++;
                var t = dResults.Rows[row][5].ToString();
                Assert.AreEqual("0100034236", dResults.Rows[row][0]);
                Assert.AreEqual("E0008", dResults.Rows[row][1]);
                Assert.AreEqual(DateTime.Parse("2012-12-18 00:01:01"), dResults.Rows[row][3]);
                Assert.AreEqual(DateTime.Parse("2012-12-18 00:01:01"), dResults.Rows[row][4]);
                Assert.IsTrue(DateTime.Parse(dResults.Rows[row][5].ToString())> now);
                Assert.AreEqual("MMANUEL", dResults.Rows[row][7]);

                // this will test tickets with out of order states

                da.SelectCommand.CommandText = "SELECT * FROM dbo.SRS_Ticket_LifeCycle_Stage_Cleaned WHERE Ticket_Number LIKE '0100034274'";

                dResults.Clear();
                da.Fill(dResults);

                row = 0;
                Assert.AreEqual("E0001", dResults.Rows[row][1]);
                Assert.AreEqual(0,int.Parse(dResults.Rows[row][6].ToString()));
                Assert.AreEqual("CSCOTT", dResults.Rows[row][7]);

                row++; ;
                Assert.AreEqual("E0008", dResults.Rows[row][1]);
                Assert.AreEqual("CSCOTT", dResults.Rows[row][7]);

                da.SelectCommand.CommandText = "SELECT * FROM dbo.SRS_Ticket_LifeCycle_Stage_Cleaned WHERE Ticket_Number LIKE '0100034295'";

                dResults.Clear();
                da.Fill(dResults);

                row = 0;
                Assert.AreEqual("E0001", dResults.Rows[row][1]);
                Assert.AreEqual(0, int.Parse(dResults.Rows[row][6].ToString()));
                Assert.AreEqual("RMOROZ", dResults.Rows[row][7]);

                row++; ;
                Assert.AreEqual("E0007", dResults.Rows[row][1]);
                Assert.AreEqual("RMOROZ", dResults.Rows[row][7]);
                
                
                //This will test that records that have last state discrepency are not transferred

                da.SelectCommand.CommandText =
                    @"SELECT Ticket_Number FROM dbo.SRS_Ticket_LifeCycle_Stage_Cleaned " +
                    @"WHERE Ticket_Number IN (SELECT st.Ticket_Number FROM " +
                    @"(SELECT [Ticket_Number],[Status] FROM dbo.SRS_Ticket_LifeCycle_Stage " +
                    @"WHERE Ticket_Number IN (SELECT [Ticket_Number] " +
                    @"FROM [dbo].[SRS_Ticket_LifeCycle_Stage] GROUP BY Ticket_Number " +
                    @"HAVING COUNT([Status])=1 )) st INNER JOIN (SELECT DISTINCT Ticket_Number, Action, Ticket_Status " +
                    @"FROM dbo.SRS_Ticket_Transaction_Stage) rt ON st.Ticket_Number LIKE rt.Ticket_Number " +
                    @"WHERE Status LIKE 'E0001' AND Ticket_Status LIKE 'Closed')";


                dResults.Clear();
                da.Fill(dResults);

                Assert.AreEqual(0, dResults.Rows.Count);

                da.SelectCommand.CommandText = @"SELECT [Table_Name],[Package_Name],[Execution_Start_Time],[Execution_End_Time],[Processing_Successful], Audit_Key," +
                    @"[Table_Initial_Row_Count],[Table_Final_Row_Count] FROM [SRS_Dim_Audit] WHERE Package_Name LIKE 'SAPSRSTicketLifeCycleStageToStageCleaned'";

                da.Fill(dAudit);
                var r = dAudit.Rows[0][2].ToString();

                Assert.AreEqual(1, dAudit.Rows.Count);
                Assert.AreEqual("SRS_Ticket_LifeCycle_Stage_Cleaned", dAudit.Rows[0][0].ToString());
                Assert.AreEqual("SAPSRSTicketLifeCycleStageToStageCleaned", dAudit.Rows[0][1].ToString());
                Assert.AreEqual(bool.TrueString, dAudit.Rows[0][4].ToString());
                Assert.IsTrue(int.Parse(dAudit.Rows[0][7].ToString()) > 10);

                Assert.AreEqual(9563,int.Parse(dAudit.Rows[0][7].ToString()));

                sqlConn.Close();
            }
        }

        [TestMethod]
        public void SRSTimeOnTicketDataCleanTest()
        {
            using (SqlConnection sqlConn = new
               SqlConnection(@"Data Source=localhost;Initial Catalog=DB;Integrated Security=SSPI;"))
            {

                StringBuilder str = new StringBuilder();
                str.Append(@"SELECT * FROM [dbo].[SRS_Time_On_Ticket_Stage_Cleaned]");
                sqlConn.Open();
                SqlDataAdapter da = new SqlDataAdapter(str.ToString(), sqlConn);
                DataTable dResults = new DataTable();
                DataTable dAudit = new DataTable();
                da.Fill(dResults);
                Assert.IsTrue(dResults.Rows.Count > 50);

                DataTable dtOriginal = new DataTable();
                da.SelectCommand.CommandText = @"SELECT * FROM [dbo].[SRS_Time_On_Ticket_Stage]";
                da.Fill(dtOriginal);

              
                da.SelectCommand.CommandText = @"SELECT [Table_Name],[Package_Name],[Execution_Start_Time],[Execution_End_Time],[Processing_Successful], Audit_Key," +
                    @"[Table_Initial_Row_Count],[Table_Final_Row_Count] FROM [SRS_Dim_Audit] WHERE Package_Name LIKE 'SAPSRSTimeOnTicketDataToStageCleaned'";

                da.Fill(dAudit);
                var r = dAudit.Rows[0][2].ToString();

                Assert.AreEqual(1, dAudit.Rows.Count);
                Assert.AreEqual("SRS_Time_On_Ticket_Stage_Cleaned", dAudit.Rows[0][0].ToString());
                Assert.AreEqual("SAPSRSTimeOnTicketDataToStageCleaned", dAudit.Rows[0][1].ToString());
                Assert.AreEqual(bool.TrueString, dAudit.Rows[0][4].ToString());
                Assert.IsTrue(int.Parse(dAudit.Rows[0][7].ToString()) > 10);

                Assert.AreEqual(int.Parse(dAudit.Rows[0][7].ToString()), dResults.Rows.Count);

                sqlConn.Close();
            }
        }


AFTER

       namespace Integration_Services_Tests
       {
          [TestClass]
         public class UnitTest1
         {
        public static DateTime now;
        [TestMethod]
        public void SRSTransactiondataCleanTest()
        {
            using (var context = new DB())
            {
                var stageCleaned = context.SRS_Ticket_Transaction_Stage_Cleaned;
                var auditRecords = context.SRS_Dim_Audit.Where(s => s.Package_Name == "SAPSRSDataToStageClean");
                var auditRecord = auditRecords.Single();

                Assert.IsTrue(stageCleaned.Count() > 50);
                Assert.AreEqual(auditRecord.Table_Final_Row_Count, stageCleaned.Count());
                Assert.AreEqual(1, auditRecords.Count());
                Assert.AreEqual("SRS_Ticket_Transaction_Stage_Cleaned", auditRecord.Table_Name);
                Assert.AreEqual("SAPSRSDataToStageClean", auditRecord.Package_Name);
                Assert.AreEqual(true, auditRecord.Processing_Successful);
                Assert.IsTrue(auditRecord.Table_Final_Row_Count > 10);
            }
        }

        [TestMethod]
        public void SRSLifeCycleDataCleanTest()
        {
            using (var context = new DB())
            {

                var stageCleaned = context.SRS_Ticket_LifeCycle_Stage_Cleaned;
                var dAudit = context.SRS_Ticket_LifeCycle_Stage_Cleaned.Where(s => s.Ticket_Number == "0100034236").ToList();
                var dResults = stageCleaned.ToList();
                dAudit[0].Ticket_Number.ToArray();

                int row = 22;
                Assert.AreEqual("0100034236", dResults[row].Ticket_Number);
                Assert.AreEqual("E0001", dResults[row].Status);
                Assert.AreEqual(DateTime.Parse("2012-12-01 08:57:16"), dResults[row].TimeStamp);
                Assert.AreEqual(DateTime.Parse("2012-12-01 08:57:16"), dResults[row].Start_Time);
                Assert.AreEqual(DateTime.Parse("2012-12-03 16:25:47"), dResults[row].End_Time);
                Assert.AreEqual((DateTime.Parse("2012-12-03 16:25:47") - DateTime.Parse("2012-12-01 08:57:16")).TotalSeconds,
                    int.Parse(dResults[row].Duration.ToString()));
                Assert.AreEqual("SGILDER", dResults[row].User);

                row++;
                Assert.AreEqual("0100034236", dResults[row].Ticket_Number);
                Assert.AreEqual("E0002", dResults[row].Status);
                Assert.AreEqual(DateTime.Parse("2012-12-03 16:25:47.000"), dResults[row].TimeStamp);
                Assert.AreEqual(DateTime.Parse("2012-12-03 16:25:47.000"), dResults[row].Start_Time);
                Assert.AreEqual(DateTime.Parse("2012-12-11 10:01:32.000"), dResults[row].End_Time);
                Assert.AreEqual((DateTime.Parse("2012-12-11 10:01:32.000") - DateTime.Parse("2012-12-03 16:25:47.000")).TotalSeconds,
                    int.Parse(dResults[row].Duration.ToString()));
                Assert.AreEqual("SPLAXTON", dResults[row].User);

                row++;
                Assert.AreEqual("0100034236", dResults[row].Ticket_Number);
                Assert.AreEqual("E0007", dResults[row].Status);
                Assert.AreEqual(DateTime.Parse("2012-12-11 10:01:32"), dResults[row].TimeStamp);
                Assert.AreEqual(DateTime.Parse("2012-12-11 10:01:32"), dResults[row].Start_Time);
                Assert.AreEqual(DateTime.Parse("2012-12-18 00:01:01"), dResults[row].End_Time);
                Assert.AreEqual((DateTime.Parse("2012-12-18 00:01:01") - DateTime.Parse("2012-12-11 10:01:32")).TotalSeconds,
                int.Parse(dResults[row].Duration.ToString()));
                Assert.AreEqual("SPLAXTON", dResults[row].User);

                row++;
                var t = dResults[row].End_Time.ToString();
                Assert.AreEqual("0100034236", dResults[row].Ticket_Number);
                Assert.AreEqual("E0008", dResults[row].Status);
                Assert.AreEqual(DateTime.Parse("2012-12-18 00:01:01"), dResults[row].TimeStamp);
                Assert.AreEqual(DateTime.Parse("2012-12-18 00:01:01"), dResults[row].Start_Time);
                Assert.IsTrue(DateTime.Parse(dResults[row].End_Time.ToString()) > now);
                Assert.AreEqual("MMANUEL", dResults[row].User);

                row = 163;
                Assert.AreEqual("E0001", dResults[row].Status);
                Assert.AreEqual(0, int.Parse(dResults[row].Duration.ToString()));
                Assert.AreEqual("CSCOTT", dResults[row].User);

                row++; ;
                Assert.AreEqual("E0008", dResults[row].Status);
                Assert.AreEqual("CSCOTT", dResults[row].User);

                row = 239;
                Assert.AreEqual("E0001", dResults[row].Status);
                Assert.AreEqual(0, int.Parse(dResults[row].Duration.ToString()));
                Assert.AreEqual("RMOROZ", dResults[row].User);

                row++; ;
                Assert.AreEqual("E0007", dResults[row].Status);
                Assert.AreEqual("RMOROZ", dResults[row].User);



                var t2 =
                    from t1 in context.SRS_Ticket_LifeCycle_Stage_Cleaned
                    where(
                    from st in context.SRS_Ticket_LifeCycle_Stage.GroupBy(g => g.Ticket_Number).Where(g => g.Count() == 1)
                    join rt in context.SRS_Ticket_Transaction_Stage on st.First().Ticket_Number equals rt.Ticket_Number
                    where st.First().Status == "E0001" && rt.Ticket_Status == "Closed"
                    select st.First().Ticket_Number
                    )
                    .Contains(t1.Ticket_Number)
                select t1;

                Assert.IsTrue(context.SRS_Ticket_LifeCycle_Stage.FirstOrDefault(rows => rows.Ticket_Number != null) != null);

                var auditRecords = context.SRS_Dim_Audit.Where(s => s.Package_Name == "SAPSRSTicketLifeCycleStageToStageCleaned");
                var auditRecord = auditRecords.Single();
                var r = auditRecord.Package_Name.ToString();

                Assert.AreEqual(1, auditRecords.Count());
                Assert.AreEqual("SRS_Ticket_LifeCycle_Stage_Cleaned", auditRecord.Table_Name);
                Assert.AreEqual("SAPSRSTicketLifeCycleStageToStageCleaned", auditRecord.Package_Name);
                Assert.AreEqual(true, auditRecord.Processing_Successful);
                Assert.IsTrue(auditRecord.Table_Final_Row_Count > 10);
                Assert.AreEqual(9563, auditRecord.Table_Final_Row_Count);

            }
        }
        
        [TestMethod]
        public void SRSTimeOnTicketDataCleanTest()
        {

            using (var context = new DB())
            {
                var auditRecordsCleaned = context.SRS_Dim_Audit.Where(s => s.Package_Name == "SAPSRSTimeOnTicketDataToStageCleaned");
                var auditRecordCleaned = auditRecordsCleaned.Single();
                var stageCleaned = context.SRS_Time_On_Ticket_Stage_Cleaned;

                Assert.AreEqual(1, auditRecordsCleaned.Count());
                Assert.AreEqual("SRS_Time_On_Ticket_Stage_Cleaned", auditRecordsCleaned.Single().Table_Name);
                Assert.AreEqual("SAPSRSTimeOnTicketDataToStageCleaned", auditRecordsCleaned.Single().Package_Name);
                Assert.AreEqual(true, auditRecordsCleaned.Single().Processing_Successful);
                Assert.IsTrue(auditRecordsCleaned.Single().Table_Final_Row_Count > 10);
                Assert.AreEqual(auditRecordCleaned.Table_Final_Row_Count, stageCleaned.Count());
            }
        }
    }
}
