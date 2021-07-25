# WickedDataAccessLayer

Example

using System;
using System.Data;
using System.Data.SqlClient;
using WickedDAL.SqlWrapper;
using System.Reflection;


/// <summary>
	/// Class written in ordinary way
	/// </summary>
	public class Orders1
	{
		private SqlConnection m_connection = null;

		public SqlConnection Connection
		{
			get{return m_connection;}
			set{m_connection = value;}
		}

		public DataSet CustOrdersDetail(int OrderID)
		{
			SqlCommand cmd = new SqlCommand("CustOrdersDetail", m_connection);
			cmd.CommandType = CommandType.StoredProcedure;
			cmd.Parameters.Add("@OrderID", SqlDbType.Int);
			cmd.Parameters["@OrderID"].Value = OrderID;
			SqlDataAdapter da = new SqlDataAdapter(cmd);
			DataSet ds = new DataSet();
			da.Fill(ds);
			return ds;
		}

		public int CountByEmployee(int EmployeeID)
		{
			SqlCommand cmd = new SqlCommand(
				"select count(*) from Orders where EmployeeID=@EmployeeID", 
				m_connection);
			cmd.CommandType = CommandType.Text;
			cmd.Parameters.Add("@EmployeeID", SqlDbType.Int);
			cmd.Parameters["@EmployeeID"].Value = EmployeeID;
			int count = (int)cmd.ExecuteScalar();
			return count;
		}
		
	}
  
  /// <summary>
	/// Wrapper class
	/// </summary>
	public abstract class Orders2 : SqlWrapperBase
	{
		public abstract DataSet CustOrdersDetail(int OrderID);

		[SWCommand("select count(*) from Orders where EmployeeID=@EmployeeID")]
		public abstract int CountByEmployee(int EmployeeID);

	}
	
	/// <summary>
	/// Example of implementation of class Orders2 generated by WrapFactory
	/// </summary>
	public class _ImplOrders2 : SqlWrapperBase
	{
		public DataSet CustOrdersDetail(int OrderID)
		{
			MethodInfo method = (MethodInfo)MethodBase.GetCurrentMethod();
			object[] values = new object[1];
			values[0] = OrderID;
			object obj = SWExecutor.ExecuteMethodAndGetResult(
				m_connection, 
				m_transaction, 
				method, 
				values, 
				m_autoCloseConnection);

			return (DataSet)obj;
		}

		[SWCommand("select count(*) from Orders where EmployeeID=@EmployeeID")]
		public int CountByEmployee(int EmployeeID)
		{
			MethodInfo method = (MethodInfo)MethodBase.GetCurrentMethod();
			object[] values = new object[1];
			values[0] = EmployeeID;
			object obj = SWExecutor.ExecuteMethodAndGetResult(
				m_connection, 
				m_transaction, 
				method, 
				values, 
				m_autoCloseConnection);

			return (int)obj;
		}
	}
	
	/// <summary>
	/// Demonstration of InsertUpdate command type
	/// </summary>
	public abstract class InsertUpdateDemo : SqlWrapperBase
	{
		[SWCommand(SWCommandType.InsertUpdate, "Shippers")]
		public abstract void ShippersInsertUpdate
			(
			[SWParameter(SWParameterType.Identity)]ref int ShipperID,
			[SWParameter(40)]string CompanyName,
			[SWParameter(24)]string Phone
			);

		[SWCommand(SWCommandType.InsertUpdate, "Order Details")]
		public abstract void OrderDetailsInsertUpdate
			(
			[SWParameter(SWParameterType.Key)]int OrderID,
			[SWParameter(SWParameterType.Key)]int ProductID,
			Decimal UnitPrice,
			Int16 Quantity,
			float Discount
			);
		
		[SWCommand("select * from Shippers where ShipperID=@ShipperID")]
		public abstract DataTable ShippersSelect(int ShipperID);

		[SWCommand("select * from [Order Details] where OrderID=@OrderID and ProductID=@ProductID")]
		public abstract DataTable OrderDetailsSelect(int OrderID, int ProductID);

		[SWCommand("delete from Shippers where ShipperID=@ShipperID")]
		public abstract void ShippersDelete(int ShipperID);

		[SWCommand("delete from [Order Details] where OrderID=@OrderID and ProductID=@ProductID")]
		public abstract void OrderDetailsDelete(int OrderID, int ProductID);
	}

	/// <summary>
	/// Some other wrapper class
	/// </summary>
	public abstract class UserClass1 : SqlWrapperBase
	{
		/// <summary>
		/// A method to be generated by WrapFactory
		/// </summary>
		/// <returns></returns>
		[SWCommand("select * from Categories")]
		public abstract DataTable Method1();

		/// <summary>
		/// This method will stay as is
		/// </summary>
		/// <returns></returns>
		public string[] Method2()
		{
			DataTable dt = Method1();
			string[] s = new string[dt.Rows.Count];
			for(int i = 0; i < dt.Rows.Count; ++i)
			{
				s[i] = (string)dt.Rows[i]["CategoryName"];
			}
			return s;
		}

	}

	/// <summary>
	/// DAL class. Nothing can be easier.
	/// </summary>
	public class MyDAL : DataAccessLayerBase
	{
		public UserClass1 UserClass1{get{return (UserClass1)GetWrapped();}}
		public Orders2 Orders2{get{return (Orders2)GetWrapped();}}
	}
